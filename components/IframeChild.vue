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
        iframe: this.iframe,
      }
    },
    data() {
      return {
        iframe: {
          params: {},
          emit: this.postMessage,
        },
      }
    },
    destroyed() {
      window.removeEventListener('message', this.handleMessage)
    },
    mounted() {
      window.addEventListener('message', this.handleMessage)

      const resizeObserver = new ResizeObserver(() => {
        // Can't use entry.contentRect because of polyfill misbehavior
        this.postMessage('iframeHeight', document.body.offsetHeight)
      })
      resizeObserver.observe(document.body)

      window.onpopstate = () => {
        this.postMessage('iframeUrl', location.href)
      }

      this.postMessage('mounted')
    },
    methods: {
      postMessage(type, payload) {
        parent.window.postMessage(
          { fromIframe: true, type, payload },
          '*',
        )
      },

      setDataFromParent(data) {
        this.iframe.params = data;
      },

      handleMessage({ data: { fromParent, type, payload } }) {
        console.log({ fromParent, type, payload });

        if (!fromParent) {
          return
        }
        if (type === 'setData') {
          this.setDataFromParent(payload)
        }

      },
    },
  }
</script>
