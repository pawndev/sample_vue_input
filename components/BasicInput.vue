<template>
  <div class="mb-4">
    <label class="block text-gray-700 text-sm font-bold mb-2" for="email">
      Courriel
    </label>
    <input
      id="email"
      v-model="userData.email"
      class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
      type="text"
      placeholder="E-mail"
      @blur="saveData"
    />
  </div>
</template>

<script>
export default {
  data() {
    return {
      userData: {
        email: ''
      }
    }
  },
  mounted() {
    console.log({ ...localStorage })
    if (localStorage.email) {
      this.userData.email = localStorage.email
    }
  },
  methods: {
    saveData(evt) {
      localStorage.email = this.userData.email
    }
  }
}
</script>
