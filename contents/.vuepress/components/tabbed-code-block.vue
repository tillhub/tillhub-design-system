<template>
  <div class="tabbed-code-block-container">
    <header>
      <div v-for="(item, index) in languageBlocks" @click="currentTab = index" :key="index" :class="{ active: index === currentTab }">
        {{item.language.toUpperCase()}}
      </div>
    </header>
    <div ref="container" class="language-container">
      <v-nodes :vnodes="languageBlocks" :currenttab="currentTab"/>
    </div>
  </div>
</template>

<script>
  export default {
    computed: {
      codeBlocks: function () {
        return this.$slots.default.filter((item) => (item && item.data && item.data.staticClass && item.data.staticClass.indexOf('language-') !== -1))
      },
      languageBlocks: function () {
        return this.codeBlocks.map((item) => {
          const language = /(?:language)(?:-)(\w+)/g.exec(item.data.staticClass)
          // original was, but does not work in safari
          // const language = item.data.staticClass.match(/(?<=\blanguage-)(\w+)/g)

          return {
            language: language ? language[1] : null,
            item
          }
        }).filter((item) => (item.language))
      },
    },
    data() {
      return {
        currentTab: 0
      }
    },
    components: {
      VNodes: {
        functional: true,
        render: (h, ctx) => {
          if (!ctx.props.vnodes || !ctx.props.vnodes.length) return
          const nodes = ctx.props.vnodes.map((item) => (item.item))
          return nodes[ctx.props.currenttab]
        }
      }
    }
  }
</script>

<style scoped>
header {
  display: flex;
}

header > div {
  width: auto;
  cursor: pointer;
  font-size: 1.28rem;
  padding: 1.2rem 1.6rem;
}

header > div.active {
  color: #03a9f4;
  border-bottom: 2px solid #03a9f4;
}

.language-container pre {
  margin-top: 0;
  border-radius: 0;
}

.tabbed-code-block-container {
  box-sizing: border-box;
  box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.14), 0 1px 5px 0 rgba(0, 0, 0, 0.12),
    0 3px 1px -2px rgba(0, 0, 0, 0.2);
}
</style>

<style>
div.language-container div[class*="language-"] {
  border-radius: 0 !important;
  border: 0;
}
</style>
