<template>
  <div class="contact">
    <list :list='lcontacts' :disable="disableList" title="Contacts" @select='onSelect' @option='onOption'></list>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import { generateColorForStr } from '@/Utils'
import List from './../List.vue'
import Modal from '@/components/Modal/index.js'

export default {
  components: {
    List
  },
  data () {
    return {
      disableList: false
    }
  },
  computed: {
    ...mapGetters(['contacts']),
    lcontacts: function () {
      let addContact = {display: 'Ajouter un contact', letter: '+', num: '', id: -1}
      return [addContact, ...this.contacts.map(e => {
        e.backgroundColor = generateColorForStr(e.number)
        return e
      })]
    }
  },
  methods: {
    onSelect: function (contact) {
      if (contact.id === -1) {
        this.$router.push({ name: 'contacts.view', params: { id: contact.id } })
      } else {
        this.$router.push({ name: 'messages.view', params: { number: contact.number, display: contact.display } })
      }
    },
    onOption: function (contact) {
      this.disableList = true
      Modal.CreateModal({
        choix: [
          {id: 1, title: 'Modifier le contact', icons: 'fa-circle-o', color: 'orange'},
          {id: 3, title: 'Annuler', icons: 'fa-undo'}
        ]
      }).then(rep => {
        if (rep.id === 1) {
          this.$router.push({path: 'contact/' + contact.id})
        }
        this.disableList = false
      })
    },
    back: function () {
      if (this.disableList === true) return
      this.$router.push({ name: 'home' })
    }
  },
  created: function () {
    this.$bus.$on('keyUpBackspace', this.back)
  },

  beforeDestroy: function () {
    this.$bus.$off('keyUpBackspace', this.back)
  }
}
</script>

<style scoped>
.contact{
  position: relative;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
}
</style>
