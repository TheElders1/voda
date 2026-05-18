<template>
  <div class="cloud">
    <div class="cloud__toolbar">
      <div class="cloud__toolbar-left">
        <h2>Cloud-Speicher</h2>
        <span class="cloud__usage">3,5 GB von 10 GB verwendet</span>
      </div>
      <div class="cloud__toolbar-right">
        <div class="cloud__search">
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="11" cy="11" r="8"/><line x1="21" y1="21" x2="16.65" y2="16.65"/></svg>
          <input type="text" v-model="search" placeholder="Dateien durchsuchen..." />
        </div>
        <button class="cloud__upload-btn" @click="uploading = true">
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M21 15v4a2 2 0 01-2 2H5a2 2 0 01-2-2v-4"/><polyline points="17 8 12 3 7 8"/><line x1="12" y1="3" x2="12" y2="15"/></svg>
          Hochladen
        </button>
      </div>
    </div>

    <div class="cloud__view-toggle">
      <button
        class="cloud__view-btn"
        :class="{ active: viewMode === 'grid' }"
        @click="viewMode = 'grid'"
      >
        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="3" y="3" width="7" height="7"/><rect x="14" y="3" width="7" height="7"/><rect x="3" y="14" width="7" height="7"/><rect x="14" y="14" width="7" height="7"/></svg>
      </button>
      <button
        class="cloud__view-btn"
        :class="{ active: viewMode === 'list' }"
        @click="viewMode = 'list'"
      >
        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><line x1="8" y1="6" x2="21" y2="6"/><line x1="8" y1="12" x2="21" y2="12"/><line x1="8" y1="18" x2="21" y2="18"/><line x1="3" y1="6" x2="3.01" y2="6"/><line x1="3" y1="12" x2="3.01" y2="12"/><line x1="3" y1="18" x2="3.01" y2="18"/></svg>
      </button>
    </div>

    <div class="cloud__folders" v-if="!search">
      <h3>Ordner</h3>
      <div class="cloud__folder-grid">
        <div v-for="folder in folders" :key="folder.name" class="cloud__folder" @click="currentFolder = folder.name">
          <div class="cloud__folder-icon" v-html="folder.icon"></div>
          <span class="cloud__folder-name">{{ folder.name }}</span>
          <span class="cloud__folder-count">{{ folder.count }} Dateien</span>
        </div>
      </div>
    </div>

    <div class="cloud__files">
      <h3>{{ currentFolder || 'Alle Dateien' }}</h3>
      <div v-if="viewMode === 'grid'" class="cloud__file-grid">
        <div v-for="file in filteredFiles" :key="file.id" class="cloud__file-card">
          <div class="cloud__file-preview" :class="'cloud__file-preview--' + file.type">
            <span v-html="fileIcon(file.type)"></span>
          </div>
          <div class="cloud__file-info">
            <span class="cloud__file-name">{{ file.name }}</span>
            <span class="cloud__file-meta">{{ file.size }} &middot; {{ file.date }}</span>
          </div>
        </div>
      </div>
      <div v-else class="cloud__file-list">
        <div v-for="file in filteredFiles" :key="file.id" class="cloud__file-row">
          <span class="cloud__file-row-icon" v-html="fileIcon(file.type)"></span>
          <span class="cloud__file-row-name">{{ file.name }}</span>
          <span class="cloud__file-row-size">{{ file.size }}</span>
          <span class="cloud__file-row-date">{{ file.date }}</span>
          <button class="cloud__file-row-menu">
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><circle cx="12" cy="12" r="1"/><circle cx="12" cy="5" r="1"/><circle cx="12" cy="19" r="1"/></svg>
          </button>
        </div>
      </div>
    </div>

    <div class="cloud__upload-overlay" v-if="uploading" @click.self="uploading = false">
      <div class="cloud__upload-card">
        <h3>Datei hochladen</h3>
        <div class="cloud__upload-dropzone">
          <svg width="40" height="40" viewBox="0 0 24 24" fill="none" stroke="var(--vf-gray-300)" stroke-width="1.5"><path d="M21 15v4a2 2 0 01-2 2H5a2 2 0 01-2-2v-4"/><polyline points="17 8 12 3 7 8"/><line x1="12" y1="3" x2="12" y2="15"/></svg>
          <p>Dateien hierher ziehen oder klicken</p>
        </div>
        <button class="cloud__upload-close" @click="uploading = false">Schliessen</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

const search = ref('');
const viewMode = ref('grid');
const currentFolder = ref('');
const uploading = ref(false);

const folders = [
  { name: 'Dokumente', count: 12, icon: '<svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="#006dfe" stroke-width="2"><path d="M14 2H6a2 2 0 00-2 2v16a2 2 0 002 2h12a2 2 0 002-2V8z"/><polyline points="14 2 14 8 20 8"/></svg>' },
  { name: 'Bilder', count: 48, icon: '<svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="#008744" stroke-width="2"><rect x="3" y="3" width="18" height="18" rx="2"/><circle cx="8.5" cy="8.5" r="1.5"/><polyline points="21 15 16 10 5 21"/></svg>' },
  { name: 'Musik', count: 8, icon: '<svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="#ffa700" stroke-width="2"><path d="M9 18V5l12-2v13"/><circle cx="6" cy="18" r="3"/><circle cx="18" cy="16" r="3"/></svg>' },
  { name: 'Videos', count: 3, icon: '<svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="#e60000" stroke-width="2"><polygon points="23 7 16 12 23 17 23 7"/><rect x="1" y="5" width="15" height="14" rx="2"/></svg>' }
];

const files = ref([
  { id: 1, name: 'Jahresbericht_2026.pdf', type: 'pdf', size: '2,4 MB', date: '17.05.2026' },
  { id: 2, name: 'Urlaubsfoto.jpg', type: 'image', size: '3,1 MB', date: '15.05.2026' },
  { id: 3, name: 'Prasentation.pptx', type: 'doc', size: '5,8 MB', date: '12.05.2026' },
  { id: 4, name: 'Budget.xlsx', type: 'doc', size: '1,2 MB', date: '10.05.2026' },
  { id: 5, name: 'Sommerplaylist.mp3', type: 'audio', size: '4,5 MB', date: '08.05.2026' },
  { id: 6, name: 'Vertrag.pdf', type: 'pdf', size: '890 KB', date: '05.05.2026' },
  { id: 7, name: 'Familienfoto.png', type: 'image', size: '2,8 MB', date: '01.05.2026' },
  { id: 8, name: 'Urlaubsvideo.mp4', type: 'video', size: '45 MB', date: '28.04.2026' }
]);

const filteredFiles = computed(() => {
  if (search.value) {
    const s = search.value.toLowerCase();
    return files.value.filter(f => f.name.toLowerCase().includes(s));
  }
  return files.value;
});

function fileIcon(type) {
  const icons = {
    pdf: '<svg width="32" height="32" viewBox="0 0 24 24" fill="none" stroke="#e60000" stroke-width="1.5"><path d="M14 2H6a2 2 0 00-2 2v16a2 2 0 002 2h12a2 2 0 002-2V8z"/><polyline points="14 2 14 8 20 8"/></svg>',
    image: '<svg width="32" height="32" viewBox="0 0 24 24" fill="none" stroke="#008744" stroke-width="1.5"><rect x="3" y="3" width="18" height="18" rx="2"/><circle cx="8.5" cy="8.5" r="1.5"/><polyline points="21 15 16 10 5 21"/></svg>',
    doc: '<svg width="32" height="32" viewBox="0 0 24 24" fill="none" stroke="#006dfe" stroke-width="1.5"><path d="M14 2H6a2 2 0 00-2 2v16a2 2 0 002 2h12a2 2 0 002-2V8z"/><polyline points="14 2 14 8 20 8"/></svg>',
    audio: '<svg width="32" height="32" viewBox="0 0 24 24" fill="none" stroke="#ffa700" stroke-width="1.5"><path d="M9 18V5l12-2v13"/><circle cx="6" cy="18" r="3"/><circle cx="18" cy="16" r="3"/></svg>',
    video: '<svg width="32" height="32" viewBox="0 0 24 24" fill="none" stroke="#e60000" stroke-width="1.5"><polygon points="23 7 16 12 23 17 23 7"/><rect x="1" y="5" width="15" height="14" rx="2"/></svg>'
  };
  return icons[type] || icons.doc;
}
</script>

<style scoped>
.cloud {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.cloud__toolbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
  gap: 12px;
}

.cloud__toolbar h2 {
  font-size: 20px;
  font-weight: 600;
  color: var(--vf-gray-900);
}

.cloud__usage {
  font-size: 13px;
  color: var(--vf-gray-500);
  margin-left: 8px;
}

.cloud__toolbar-right {
  display: flex;
  gap: 12px;
  align-items: center;
}

.cloud__search {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 12px;
  background: white;
  border: 1px solid var(--vf-gray-200);
  border-radius: var(--vf-radius);
}

.cloud__search:focus-within {
  border-color: var(--vf-red);
}

.cloud__search input {
  border: none;
  outline: none;
  font-size: 14px;
  width: 180px;
  background: none;
}

.cloud__upload-btn {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 8px 16px;
  background: var(--vf-red);
  color: white;
  border: none;
  border-radius: var(--vf-radius);
  font-size: 14px;
  font-weight: 500;
  transition: all var(--vf-transition);
}

.cloud__upload-btn:hover {
  background: var(--vf-red-dark);
}

.cloud__view-toggle {
  display: flex;
  gap: 4px;
}

.cloud__view-btn {
  padding: 6px;
  border: 1px solid var(--vf-gray-200);
  background: white;
  border-radius: var(--vf-radius);
  color: var(--vf-gray-400);
  transition: all var(--vf-transition);
}

.cloud__view-btn.active {
  background: var(--vf-gray-100);
  color: var(--vf-gray-700);
  border-color: var(--vf-gray-300);
}

.cloud__folders h3,
.cloud__files h3 {
  font-size: 15px;
  font-weight: 600;
  color: var(--vf-gray-700);
  margin-bottom: 12px;
}

.cloud__folder-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(160px, 1fr));
  gap: 12px;
}

.cloud__folder {
  background: white;
  padding: 16px;
  border-radius: var(--vf-radius);
  box-shadow: var(--vf-shadow);
  cursor: pointer;
  transition: all var(--vf-transition);
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
  text-align: center;
}

.cloud__folder:hover {
  transform: translateY(-2px);
  box-shadow: var(--vf-shadow-lg);
}

.cloud__folder-icon {
  display: flex;
  align-items: center;
  justify-content: center;
}

.cloud__folder-name {
  font-size: 14px;
  font-weight: 500;
  color: var(--vf-gray-900);
}

.cloud__folder-count {
  font-size: 12px;
  color: var(--vf-gray-400);
}

.cloud__file-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(180px, 1fr));
  gap: 12px;
}

.cloud__file-card {
  background: white;
  border-radius: var(--vf-radius);
  box-shadow: var(--vf-shadow);
  overflow: hidden;
  transition: all var(--vf-transition);
  cursor: pointer;
}

.cloud__file-card:hover {
  transform: translateY(-2px);
  box-shadow: var(--vf-shadow-lg);
}

.cloud__file-preview {
  height: 100px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: var(--vf-gray-50);
}

.cloud__file-preview--pdf { background: #fef2f2; }
.cloud__file-preview--image { background: #f0fdf4; }
.cloud__file-preview--doc { background: #eff6ff; }
.cloud__file-preview--audio { background: #fffbeb; }
.cloud__file-preview--video { background: #fef2f2; }

.cloud__file-info {
  padding: 10px 12px;
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.cloud__file-name {
  font-size: 13px;
  font-weight: 500;
  color: var(--vf-gray-900);
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.cloud__file-meta {
  font-size: 11px;
  color: var(--vf-gray-400);
}

.cloud__file-list {
  background: white;
  border-radius: var(--vf-radius-lg);
  box-shadow: var(--vf-shadow);
  overflow: hidden;
}

.cloud__file-row {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 10px 16px;
  border-bottom: 1px solid var(--vf-gray-100);
  transition: background var(--vf-transition);
}

.cloud__file-row:last-child {
  border-bottom: none;
}

.cloud__file-row:hover {
  background: var(--vf-gray-50);
}

.cloud__file-row-icon {
  display: flex;
  align-items: center;
}

.cloud__file-row-name {
  flex: 1;
  font-size: 14px;
  color: var(--vf-gray-900);
}

.cloud__file-row-size,
.cloud__file-row-date {
  font-size: 12px;
  color: var(--vf-gray-400);
  width: 80px;
}

.cloud__file-row-menu {
  border: none;
  background: none;
  color: var(--vf-gray-400);
  padding: 4px;
  border-radius: 50%;
  transition: all var(--vf-transition);
}

.cloud__file-row-menu:hover {
  background: var(--vf-gray-100);
  color: var(--vf-gray-700);
}

.cloud__upload-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 200;
  padding: 16px;
}

.cloud__upload-card {
  background: white;
  border-radius: var(--vf-radius-lg);
  padding: 24px;
  width: 100%;
  max-width: 480px;
  text-align: center;
}

.cloud__upload-card h3 {
  font-size: 16px;
  font-weight: 600;
  margin-bottom: 16px;
}

.cloud__upload-dropzone {
  border: 2px dashed var(--vf-gray-300);
  border-radius: var(--vf-radius-lg);
  padding: 40px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
  cursor: pointer;
  transition: border-color var(--vf-transition);
}

.cloud__upload-dropzone:hover {
  border-color: var(--vf-red);
}

.cloud__upload-dropzone p {
  font-size: 14px;
  color: var(--vf-gray-500);
}

.cloud__upload-close {
  margin-top: 16px;
  padding: 8px 20px;
  border: 1px solid var(--vf-gray-200);
  background: white;
  border-radius: var(--vf-radius);
  font-size: 14px;
  color: var(--vf-gray-600);
}

@media (max-width: 768px) {
  .cloud__search input { width: 120px; }
  .cloud__toolbar { flex-direction: column; align-items: flex-start; }
}
</style>
