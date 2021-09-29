<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <h2>Essential Links</h2>
    <ul>
      <li>
        <a
          href="https://vuejs.org"
          target="_blank"
        >
          Core Docs
        </a>
      </li>
      <li>
        <a
          href="https://forum.vuejs.org"
          target="_blank"
        >
          Forum
        </a>
      </li>
      <li>
        <a
          href="https://chat.vuejs.org"
          target="_blank"
        >
          Community Chat
        </a>
      </li>
      <li>
        <a
          href="https://twitter.com/vuejs"
          target="_blank"
        >
          Twitter
        </a>
      </li>
      <br>
      <li>
        <a
          href="http://vuejs-templates.github.io/webpack/"
          target="_blank"
        >
          Docs for This Template
        </a>
      </li>
    </ul>
    <h2>Ecosystem</h2>
    <ul>
      <li>
        <a
          href="http://router.vuejs.org/"
          target="_blank"
        >
          vue-router
        </a>
      </li>
      <li>
        <a
          href="http://vuex.vuejs.org/"
          target="_blank"
        >
          vuex
        </a>
      </li>
      <li>
        <a
          href="http://vue-loader.vuejs.org/"
          target="_blank"
        >
          vue-loader
        </a>
      </li>
      <li>
        <a
          href="https://github.com/vuejs/awesome-vue"
          target="_blank"
        >
          awesome-vue
        </a>
      </li>
    </ul>
  </div>
</template>

<script>
import { CapacitorDataStorageSqlite } from 'capacitor-data-storage-sqlite'
import { Capacitor } from '@capacitor/core'

export default {
  name: 'HelloWorld',
  async mounted () {
    const storage = CapacitorDataStorageSqlite
    const platform = Capacitor.getPlatform()
    var isStore = true
    try {
      await storage.openStore({database: 'default_test', table: 'default_table', encrypted: false, mode: 'no-encryption'})
      if (platform !== 'web') {
        var res = await storage.isStoreExists({database: 'default_test'})
        if (!res.result) {
          isStore = false
          console.log(`Default Test Error: 'default_test' does not exist`)
          return
        } else {
          res = await storage.isStoreOpen({database: 'default_test'})
          if (!res.result) {
            isStore = false
            console.log(`Default Test Error: 'default_test' is not opened`)
            return
          }
        }
      }
      if (isStore) {
        await storage.set({key: 'session', value: 'opened'})
        const data = {a: 20, b: 'Hello World', c: {c1: 40, c2: 'cool'}}
        await storage.set({key: 'json', value: JSON.stringify(data)})
        var value = await storage.get({key: 'session'})
        console.log(`key session value: ${JSON.stringify(value)}`)
        if (value.value !== 'opened') {
          console.log(`Default Test Error: get 'session' failed`)
          return
        }
        value = await storage.get({key: 'json'})
        if (value.value !== JSON.stringify(data)) {
          console.log(`Default Test Error: get 'json' failed`)
          return
        }
        await storage.set({key: 'app', value: 'successful'})
        res = await storage.iskey({key: 'app'})
        if (!res.result) {
          console.log(`Default Test Error: isKey 'app' failed`)
          return
        }
        var keys = await storage.keys()
        console.log(`keys: ${keys.keys}`)
        if (keys.keys.length !== 3 || keys.keys[0] !== 'app' ||
            keys.keys[1] !== 'json' || keys.keys[2] !== 'session') {
          console.log(`Default Test Error: keys failed`)
          return
        }
        var values = await storage.values()
        console.log(`values: ${values.values}`)
        if (values.values.length !== 3 || values.values[0] !== 'successful' ||
            values.values[1] !== JSON.stringify(data) || values.values[2] !== 'opened') {
          console.log(`Default Test Error: keys failed`)
          return
        }
        if (platform !== 'web') {
          await storage.closeStore({database: 'default_test'})
        }
        console.log(`Default Test is successful`)
      } else {
        console.log(`Default Test Error: store 'default_test' does not exist or is not opened`)
      }
    } catch (err) {
      console.log(`Default Test Error: ${err}`)
    }
  },
  data () {
    return {
      msg: 'Welcome to Your Vue.js App'
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
