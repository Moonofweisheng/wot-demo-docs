<script setup lang="ts">
import { useRoute } from 'vitepress'
import { computed, ref } from 'vue'
import VPDocAside from 'vitepress/dist/client/theme-default/components/VPDocAside.vue'
import VPDocFooter from 'vitepress/dist/client/theme-default/components/VPDocFooter.vue'
import { useData } from 'vitepress';
import { useSidebar } from 'vitepress/theme';
import VPIframe from './VPIframe.vue'

const { theme }: any = useData()

const route = useRoute()
const { hasSidebar, hasAside, leftAside } = useSidebar()

const pageName = computed(() =>
  route.path.replace(/[./]+/g, '_').replace(/_html$/, '')
)
const isComponent = computed(() => {
  if (typeof route.data?.frontmatter?.iframe !== 'undefined') {
    return route.data.frontmatter.iframe
  }
  return false
})
const expanded = ref(true)

const iframeUrl = computed(() => {
  if (typeof route.data?.frontmatter?.url !== 'undefined') {
    return route.data.frontmatter.url
  }
  return ''
})

</script>

<template>

  <div class="VPDoc"
    :class="{ 'has-sidebar': hasSidebar, 'has-aside': hasAside, 'is-component': isComponent, 'is-expanded': expanded }">
    <slot name="doc-top" />
    <div class="container">
      <div v-if="hasAside" class="aside" :class="{ 'left-aside': leftAside }">
        <div class="aside-curtain" />
        <div class="aside-container">
          <div class="aside-content">
            <VPDocAside>
              <template #aside-top>
                <slot name="aside-top" />
              </template>
              <template #aside-bottom>
                <slot name="aside-bottom" />
              </template>
              <template #aside-outline-before>
                <slot name="aside-outline-before" />
              </template>
              <template #aside-outline-after>
                <slot name="aside-outline-after" />
              </template>
              <template #aside-ads-before>
                <slot name="aside-ads-before" />
              </template>
              <template #aside-ads-after>
                <slot name="aside-ads-after" />
              </template>
            </VPDocAside>
          </div>
        </div>
      </div>

      <div class="content">
        <div class="content-container">
          <slot name="doc-before" />
          <main class="main">
            <Content class="vp-doc" :class="[
              pageName,
              theme.externalLinkIcon && 'external-link-icon-enabled'
            ]" />
            <VPIframe v-if="isComponent" v-model:expanded="expanded" :url="iframeUrl" />
          </main>
          <VPDocFooter>
            <template #doc-footer-before>
              <slot name="doc-footer-before" />
            </template>
          </VPDocFooter>
          <slot name="doc-after" />
        </div>
      </div>
    </div>
    <slot name="doc-bottom" />
  </div>
</template>

<style scoped>
.VPDoc {
  padding: 32px 24px 96px;
  width: 100%;
}

@media (min-width: 768px) {
  .VPDoc {
    padding: 48px 32px 128px;
  }
}

@media (min-width: 960px) {
  .VPDoc {
    padding: 48px 32px 0;
  }

  .VPDoc:not(.has-sidebar) .container {
    display: flex;
    justify-content: center;
    max-width: 992px;
  }

  .VPDoc:not(.has-sidebar) .content {
    max-width: 752px;
  }
}

@media (min-width: 1280px) {
  .VPDoc .container {
    display: flex;
    justify-content: center;
  }

  .VPDoc .aside {
    display: block;
  }
}

@media (min-width: 1440px) {
  .VPDoc:not(.has-sidebar) .content {
    max-width: 784px;
  }

  .VPDoc:not(.has-sidebar) .container {
    max-width: 1104px;
  }
}

.container {
  margin: 0 auto;
  width: 100%;
}

.aside {
  position: relative;
  display: none;
  order: 2;
  flex-grow: 1;
  padding-left: 32px;
  width: 100%;
  max-width: 256px;
}

.left-aside {
  order: 1;
  padding-left: unset;
  padding-right: 32px;
}

.aside-container {
  position: fixed;
  top: 0;
  padding-top: calc(var(--vp-nav-height) + var(--vp-layout-top-height, 0px) + var(--vp-doc-top-height, 0px) + 48px);
  width: 224px;
  height: 100vh;
  overflow-x: hidden;
  overflow-y: auto;
  scrollbar-width: none;
}

.aside-container::-webkit-scrollbar {
  display: none;
}

.aside-curtain {
  position: fixed;
  bottom: 0;
  z-index: 10;
  width: 224px;
  height: 32px;
  background: linear-gradient(transparent, var(--vp-c-bg) 70%);
}

.aside-content {
  display: flex;
  flex-direction: column;
  min-height: calc(100vh - (var(--vp-nav-height) + var(--vp-layout-top-height, 0px) + 48px));
  padding-bottom: 32px;
}

.content {
  position: relative;
  margin: 0 auto;
  width: 100%;
}

@media (min-width: 960px) {
  .content {
    padding: 0 32px 128px;
  }
}

@media (min-width: 1280px) {
  .content {
    order: 1;
    margin: 0;
    min-width: 640px;
  }
}


@media (min-width: 1280px) {
  .VPDoc.is-component.is-expanded .container {
    padding-right: 358px;
  }

  .VPDoc.is-component:not(.is-expanded) .container {
    padding-right: 48px;
  }
}

@media (min-width: 1440px) {
  .VPDoc.is-component.is-expanded .container {
    padding-right: 424px;
  }

  .VPDoc.is-component:not(.is-expanded) .container {
    padding-right: 64px;
  }

}


.content-container {
  margin: 0 auto;
}
</style>
