<script setup>
import { RouterLink } from 'vue-router';
import { useAuthStore } from '@/stores';

defineProps({
  embedUrl: { type: String, required: true },
  sourceUrl: { type: String, required: true },
  siteName: { type: String, required: true },
});

const auth = useAuthStore();
</script>

<template>
  <main class="canva-home" :aria-label="siteName">
    <nav class="canva-home-nav" aria-label="Site navigation">
      <RouterLink to="/library" class="canva-home-library">Browse materials</RouterLink>
      <div class="canva-home-actions">
        <RouterLink v-if="auth.isAdmin" to="/admin">Admin</RouterLink>
        <RouterLink v-else :to="auth.isLoggedIn ? '/my-account' : '/login'">
          {{ auth.isLoggedIn ? 'My account' : 'Log in' }}
        </RouterLink>
        <a :href="sourceUrl" target="_blank" rel="noopener noreferrer">Open in new tab</a>
      </div>
    </nav>
    <iframe
      :key="embedUrl"
      :src="embedUrl"
      :title="`${siteName} home`"
      class="canva-home-frame"
      allow="fullscreen"
      allowfullscreen
      referrerpolicy="strict-origin-when-cross-origin"
    ></iframe>
  </main>
</template>

<style scoped>
.canva-home {
  display: flex;
  flex-direction: column;
  height: 100vh;
  height: 100dvh;
  min-height: 320px;
  background: #fff;
}

.canva-home-nav {
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-shrink: 0;
  gap: 12px;
  min-height: 48px;
  padding: 8px 20px;
  border-bottom: 1px solid #e5e7eb;
  font-size: 14px;
}

.canva-home-nav a {
  color: #15334a;
  text-decoration: underline;
  text-underline-offset: 3px;
}

.canva-home-nav a:focus-visible {
  outline: 2px solid #15334a;
  outline-offset: 4px;
}

.canva-home-library {
  font-weight: 700;
}

.canva-home-actions {
  display: flex;
  align-items: center;
  gap: 20px;
}

.canva-home-frame {
  flex: 1;
  width: 100%;
  min-height: 0;
  border: 0;
}

@media (max-width: 480px) {
  .canva-home-nav {
    padding: 8px 12px;
    font-size: 12px;
  }

  .canva-home-actions {
    gap: 12px;
  }
}
</style>
