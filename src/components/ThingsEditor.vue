<template>
  <div>
    <md-toolbar>
       <md-content class="small-icon" @click="$emit('close')">
            <md-icon>clear</md-icon>
       </md-content> 
          <h4 class="md-title">{{thing.name}}</h4>
    </md-toolbar>
    <div>
     <form>    
      <vue-tabs active-tab-color="#8bae2d" 
            active-text-color="#ededed"
            type="pills"
            :start-index="1"
            direction="vertical"
      >
       <v-tab :title="$t('properties')">
        <md-field>
            <label>{{$t('uuid')}}</label>
            <md-input v-model="uuid" readonly></md-input>
        </md-field>
        <md-field>
            <label>{{$t('name')}}</label>
            <md-input required v-model="name"></md-input>
        </md-field>
        <md-field>
            <label>{{$t('description')}}</label>
            <md-input v-model="description"></md-input>
        </md-field>
        <md-field>
            <label>{{$t('protocol')}}</label>
            <md-input v-model="protocol"></md-input>
        </md-field>
        <md-field>
            <label>{{$t('address')}}</label>
            <md-input v-model="phisicalAddress"></md-input>
        </md-field>
        <md-field>
            <label>{{$t('tags')}}</label>
            <md-input v-model="tags"></md-input>
        </md-field>
        <div id="properties-tab-buttons">
           <md-button id="create-copy-button" class="md-raised">{{$t('create_copy')}}</md-button>
           <md-button id="delete-thing-button" class="md-raised"
           @click="showDeleteThingDialog($event)"
           v-bind:uuid="thing.uuid"
           v-bind:name="thing.name" 
           >{{$t('delete_thing')}}</md-button>
        </div>
       </v-tab>
       <v-tab :title="$t('appearance')">
        <md-field>
         <label>{{$t('position_x')}}</label>
         <md-input v-model="positionX" type="number"></md-input>
        </md-field>
        <md-field>
         <label>{{$t('position_y')}}</label>
         <md-input v-model="positionY" type="number"></md-input>
        </md-field>
        <md-field>
         <label>{{$t('rotation')}}</label>
         <md-input v-model="rotation" type="number"></md-input>
        </md-field>
        <md-field>
         <label>{{$t('width')}}</label>
         <md-input v-model="width" type="number" disabled></md-input>
        </md-field>
        <md-field>
         <label>{{$t('height')}}</label>
         <md-input v-model="height" type="number" disabled></md-input>
        </md-field>
        <md-field>
          <label for="font">{{$t('environment')}}</label>
          <md-select v-model="environment" name="environment" id="environment">
            <md-option v-for="env in getEnvironmentsList" :value="env.uuid" :key="env.uuid">{{env.name}}</md-option>
          </md-select>
        </md-field>
       </v-tab>
       <v-tab :title="$t('data_source')">
        <div v-for="(value, key) in thing.triggers.propertyList">
           {{value}}
        </div>
       </v-tab>
       <v-tab :title="$t('actions')">
        <div v-for="(value, key) in thing.actions.propertyList">
           {{key}}
        </div>
       </v-tab>
       <v-tab :title="$t('control_panel')">
          <div v-for="behavior in thing.behaviors">
            <span>{{behavior.name}}</span>
            <span v-if="behavior['@class'] == 'com.freedomotic.model.object.RangedIntBehavior'">add slider</span>
            <span v-if="behavior['@class'] == 'com.freedomotic.model.object.BooleanBehavior'">add switch</span>
          </div> 
       </v-tab>
       <v-tab :title="$t('automations')">
        Third tab content
       </v-tab>
      </vue-tabs>
     </form> 
    </div>
    <md-toolbar class="md-dense md-toolbar-section-end" md-elevation="1">
       <md-button class="md-primary" @click="updateThing">{{$t('update')}}</md-button>
    </md-toolbar>
  </div>
</template>

<script>
import {VueTabs, VTab} from 'vue-nav-tabs'

export default {
  name: 'ThingsEditor',
  props: [ 'thing' ],
  components: {
    VueTabs,
    VTab
  },
  computed: {
    getEnvironmentsList: function () {
      return this.$store.state.environmentsList
    }
  },
  created () {
    this.uuid = this.thing.uuid
    this.name = this.thing.name
    this.description = this.thing.description
    this.protocol = this.thing.protocol
    this.phisicalAddress = this.thing.phisicalAddress
    this.tags = this.thing.tags
    this.positionX = this.thing.representation[this.thing.currentRepresentation].offset.x
    this.positionY = this.thing.representation[this.thing.currentRepresentation].offset.y
    this.rotation = this.thing.representation[this.thing.currentRepresentation].rotation
    this.width = this.thing.representation[this.thing.currentRepresentation].scaleX
    this.height = this.thing.representation[this.thing.currentRepresentation].scaleY
    this.environment = this.thing.envUUID
  },
  data () {
    return {
      uuid: '',
      name: '',
      description: '',
      protocol: '',
      phisicalAddress: '',
      tags: [],
      positionX: '',
      positionY: '',
      rotation: '',
      width: '',
      height: '',
      environment: ''
    }
  },
  methods: {
    updateThing () {
      // retrieve data from Properties tab
      this.thing.name = this.name
      this.thing.description = this.description
      this.thing.protocol = this.protocol
      this.thing.phisicalAddress = this.phisicalAddress
      this.thing.tags = this.tags
      // retrieve data from Appearance tab
      this.thing.envUUID = this.environment
      // call API store action
      this.$store.dispatch('updateThing', this.thing.uuid, this.thing)
      // DEBUG
      alert(JSON.stringify(this.thing, null, 2))
      this.$emit('close')
    },
    cloneThing () {
      // call API store action
      this.$store.dispatch('cloneThing', this.thing.uuid)
    },
    showDeleteThingDialog (event) {
      // var uuid = event.target.getAttribute('uuid')
      var name = event.target.getAttribute('name')
      this.$modal.show('dialog', {
        title: this.$t('delete_thing'),
        text: this.$t('delete_message') + ' " ' + name + '"?',
        buttons: [
          {
            title: this.$t('cancel'),
            handler: () => {
              this.$modal.hide('dialog')
            }
          },
          {
            title: this.$t('confirm'),
            default: true,
            handler: () => {
              this.$snotify.success('Thing "' + name + '" deleted', 'INFO', {
                timeout: 3000,
                showProgressBar: false,
                closeOnClick: false,
                pauseOnHover: true
              })
              // TODO call the action to remove the thing
              this.$store.dispatch('deleteThing', this.thing.uuid)
              this.$modal.hide('dialog')
              this.$emit('close')
            }
          }
        ]
      })
    }
  }
}
</script>

<style scoped>
  
</style>
