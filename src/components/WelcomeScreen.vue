<template>
  <div class="welcome">
    <div class="welcome__hero">
      <svg width="64" height="64" viewBox="0 0 32 32" fill="none">
        <circle cx="16" cy="16" r="16" fill="#e60000"/>
        <path d="M16 8C11.58 8 8 11.58 8 16s3.58 8 8 8 8-3.58 8-8-3.58-8-8-8zm0 14c-3.31 0-6-2.69-6-6s2.69-6 6-6 6 2.69 6 6-2.69 6-6 6z" fill="white"/>
      </svg>
      <h1>Vodafone E-Mail & Cloud</h1>
      <p>Ihre E-Mails, Dateien und Kontakte - sicher und jederzeit verfugbar.</p>
      <button class="welcome__cta" @click="$emit('login')">Jetzt anmelden</button>
    </div>
    <div class="welcome__features">
      <div class="welcome__feature" v-for="f in features" :key="f.title">
        <div class="welcome__feature-icon" v-html="f.icon"></div>
        <h3>{{ f.title }}</h3>
        <p>{{ f.desc }}</p>
      </div>
    </div>
  </div>
</template>

<script setup>
defineEmits(['login']);

const features = [
  {
    title: 'E-Mail',
    desc: 'Professionelle E-Mail-Kommunikation mit Ihrem Vodafone-Konto.',
    icon: '<svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="#e60000" stroke-width="2"><rect x="2" y="4" width="20" height="16" rx="2"/><path d="M22 4L12 13 2 4"/></svg>'
  },
  {
    title: 'Cloud-Speicher',
    desc: '10 GB kostenloser Speicher fur Ihre Dateien und Fotos.',
    icon: '<svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="#e60000" stroke-width="2"><path d="M22 19a2 2 0 01-2 2H4a2 2 0 01-2-2V5a2 2 0 012-2h5l2 3h9a2 2 0 012 2z"/></svg>'
  },
  {
    title: 'Kontakte & Kalender',
    desc: 'Verwalten Sie Ihre Kontakte und Termine an einem Ort.',
    icon: '<svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="#e60000" stroke-width="2"><rect x="3" y="4" width="18" height="18" rx="2"/><line x1="16" y1="2" x2="16" y2="6"/><line x1="8" y1="2" x2="8" y2="6"/><line x1="3" y1="10" x2="21" y2="10"/></svg>'
  }
];
</script>

<style scoped>
.welcome {
  max-width: 800px;
  margin: 0 auto;
  padding: 40px 0;
}

.welcome__hero {
  text-align: center;
  padding: 48px 24px;
  background: white;
  border-radius: var(--vf-radius-lg);
  box-shadow: var(--vf-shadow);
  margin-bottom: 32px;
}

.welcome__hero h1 {
  font-size: 28px;
  font-weight: 700;
  color: var(--vf-gray-900);
  margin-top: 20px;
}

.welcome__hero p {
  font-size: 16px;
  color: var(--vf-gray-500);
  margin-top: 8px;
  max-width: 440px;
  margin-left: auto;
  margin-right: auto;
}

.welcome__cta {
  margin-top: 24px;
  padding: 12px 32px;
  background: var(--vf-red);
  color: white;
  border: none;
  border-radius: 24px;
  font-size: 16px;
  font-weight: 500;
  transition: all var(--vf-transition);
}

.welcome__cta:hover {
  background: var(--vf-red-dark);
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(230, 0, 0, 0.3);
}

.welcome__features {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap: 20px;
}

.welcome__feature {
  background: white;
  padding: 24px;
  border-radius: var(--vf-radius-lg);
  box-shadow: var(--vf-shadow);
  transition: all var(--vf-transition);
}

.welcome__feature:hover {
  transform: translateY(-2px);
  box-shadow: var(--vf-shadow-lg);
}

.welcome__feature-icon {
  width: 48px;
  height: 48px;
  background: #fef2f2;
  border-radius: var(--vf-radius);
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 16px;
}

.welcome__feature h3 {
  font-size: 16px;
  font-weight: 600;
  color: var(--vf-gray-900);
  margin-bottom: 6px;
}

.welcome__feature p {
  font-size: 14px;
  color: var(--vf-gray-500);
  line-height: 1.5;
}
</style>
