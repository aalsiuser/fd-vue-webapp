<template>
    <div v-if="rolesOpen" class="current-section">
        <md-toolbar class="md-accent">
          <md-content class="small-icon" @click="openSettings">
            <md-icon>undo</md-icon>
          </md-content>
          <md-content class="small-icon" @click="closeWindow">
            <md-icon>clear</md-icon>
          </md-content> 
          <h3 class="md-title">{{$t('roles').toUpperCase()}}</h3>
        </md-toolbar>
        
    </div>    
</template>

<script>
export default {
  computed: {
    rolesOpen: function () {
      return this.$store.state.rolesOpen
    }
  },
  data () {
    return {}
  },
  methods: {
    openSettings: function () {
      this.$store.commit('showSetup')
    },
    closeWindow: function () {
      this.$store.commit('closeSection')
    }
  }
}
</script>

<style scoped>
   .current-section {
        position: fixed;
        top: 1%;
        bottom: 1%;
        left: 1%;
        right: 1%;
        background:white;
        border-radius: 4px;
        z-index: 130;
        color: black;
        display: flex;
        align-items: center;
        flex-direction: column;
        margin: auto;
      }

   #action-container {
       text-align: center;
       cursor: pointer;
       background-color: transparent;
       margin-top: 50%;
   }  

   .small-icon {
      width: 24px;
      margin: 1%;
      cursor: pointer;
      background:transparent;
   } 
</style>
