<template>
  <div>
    <slot />
  </div>
</template>

<script>
export default {
  name: 'IframeChild',
  provide() {
    return {
      iframe: this.iframe
    }
  },
  data() {
    return {
      iframe: {
        params: {},
        emit: this.postMessage
      }
    }
  },
  mounted() {
    const resizeObserver = new ResizeObserver(() => {
      // Can't use entry.contentRect because of polyfill misbehavior
      this.postMessage('iframeHeight', document.body.offsetHeight)
    })
    resizeObserver.observe(document.body)

    window.onpopstate = () => {
      this.postMessage('iframeUrl', location.href)
    }

    const setDataFromParent = data => this.setDataFromParent(data)
    
    window.setDataFromParent = setDataFromParent

    this.postMessage('mounted', { setDataFromParent })
  },
  methods: {
    postMessage(type, payload) {
      parent.window.postMessage({ fromIframe: true, type, payload }, '*')
    },

    setDataFromParent(data) {
      this.iframe.params = { ...data }
    }
  }
}
</script>
