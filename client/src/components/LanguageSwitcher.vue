<template>
  <div class="language-switcher">
    <button
      class="language-button"
      :title="t('language.selectLanguage')"
      :aria-label="t('language.selectLanguage')"
      @click="toggleDropdown"
      @blur="handleBlur"
    >
      <svg
        width="20"
        height="20"
        viewBox="0 0 20 20"
        fill="none"
        class="globe-icon"
      >
        <circle cx="10" cy="10" r="7.5" stroke="currentColor" stroke-width="1.5"/>
        <path d="M3 10H17" stroke="currentColor" stroke-width="1.5"/>
        <path d="M10 3C10 3 7.5 5.5 7.5 10C7.5 14.5 10 17 10 17" stroke="currentColor" stroke-width="1.5"/>
        <path d="M10 3C10 3 12.5 5.5 12.5 10C12.5 14.5 10 17 10 17" stroke="currentColor" stroke-width="1.5"/>
      </svg>
      <span class="language-label">{{ localeName }}</span>
      <svg
        class="chevron"
        :class="{ 'chevron-open': isDropdownOpen }"
        width="16"
        height="16"
        viewBox="0 0 16 16"
        fill="none"
      >
        <path d="M4 6L8 10L12 6" stroke="currentColor" stroke-width="2" stroke-linecap="round"/>
      </svg>
    </button>

    <div v-if="isDropdownOpen" class="dropdown-menu">
      <button
        v-for="locale in availableLocales"
        :key="locale"
        class="dropdown-item"
        :class="{ active: currentLocale === locale }"
        @mousedown.prevent="selectLanguage(locale)"
      >
        <span class="language-name">{{ getLanguageName(locale) }}</span>
        <svg
          v-if="currentLocale === locale"
          width="18"
          height="18"
          viewBox="0 0 18 18"
          fill="none"
          class="check-icon"
        >
          <path d="M4 9L7.5 12.5L14 6" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useI18n } from '../composables/useI18n'

const { currentLocale, setLocale, availableLocales, localeName, t } = useI18n()

const isDropdownOpen = ref(false)

const languageNames = {
  en: 'English',
  ja: '日本語'
}

const getLanguageName = (locale) => {
  return languageNames[locale] || locale
}

const toggleDropdown = () => {
  isDropdownOpen.value = !isDropdownOpen.value
}

const handleBlur = () => {
  // Delay to allow mousedown events on dropdown items to fire first
  setTimeout(() => {
    isDropdownOpen.value = false
  }, 200)
}

const selectLanguage = (locale) => {
  setLocale(locale)
  isDropdownOpen.value = false
}
</script>

<style scoped>
.language-switcher {
  position: relative;
  width: 100%;
}

.language-button {
  width: 100%;
  display: flex;
  align-items: center;
  gap: var(--sp-3);
  padding: var(--sp-2) var(--sp-3);
  background: transparent;
  border: none;
  border-radius: var(--radius);
  cursor: pointer;
  transition: background-color 0.15s ease, color 0.15s ease;
  font-family: inherit;
  font-size: 0.875rem;
  color: var(--muted);
  text-align: left;
}

.language-button:hover {
  background: var(--line-soft);
  color: var(--ink);
}

.globe-icon {
  color: currentColor;
  flex-shrink: 0;
}

.language-label {
  flex: 1;
  font-weight: 500;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.chevron {
  color: currentColor;
  transition: transform 0.2s ease;
  flex-shrink: 0;
}

.chevron-open {
  transform: rotate(180deg);
}

/* Popover opens to the RIGHT of the sidebar so the rail doesn't clip it downward */
.dropdown-menu {
  position: absolute;
  left: calc(100% + 8px);
  bottom: 0;
  min-width: 160px;
  background: var(--surface);
  border: 1px solid var(--line);
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-md);
  z-index: 1000;
  overflow: hidden;
}

.dropdown-item {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: var(--sp-3);
  padding: var(--sp-3) var(--sp-4);
  background: none;
  border: none;
  text-align: left;
  cursor: pointer;
  transition: background 0.15s ease;
  font-family: inherit;
  font-size: 0.875rem;
  font-weight: 500;
  color: var(--text);
}

.dropdown-item:hover {
  background: var(--line-soft);
}

.dropdown-item.active {
  background: var(--accent-soft);
  color: var(--accent);
}

.language-name {
  flex: 1;
}

.check-icon {
  color: var(--accent);
  flex-shrink: 0;
}

/* Collapsed rail: icon-only, centered */
.app-shell.collapsed .language-switcher {
  width: auto;
}

.app-shell.collapsed .language-button {
  justify-content: center;
  padding: var(--sp-2);
}

.app-shell.collapsed .language-label,
.app-shell.collapsed .chevron {
  display: none;
}

/* Narrow viewports force the same rail layout */
@media (max-width: 1000px) {
  .language-switcher {
    width: auto;
  }

  .language-button {
    justify-content: center;
    padding: var(--sp-2);
  }

  .language-label,
  .chevron {
    display: none;
  }
}
</style>
