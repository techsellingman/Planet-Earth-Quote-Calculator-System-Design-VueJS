<template>
<div class="container">
  <div class="title">
    <h2>Settings</h2>
  </div>
  <div class="content">
    <div id="settings-floors">
      <form id="floors-page" class="">
          <div class="row mb-3">
            <div class="col-3 text-l">
              <label class="text-l">Level:</label>
            </div>
            <div class="col-9">
                <!-- :default="'Edit floor activity'" -->
              <custom-dropdown
                :options="levels"
                :default="selectedLevel"
                class="select"
                v-model="selected"
                @onAdd="addLevel($event)"
                @onDelete="confirmDeleteLevel($event)"         
                @onEdit="editLevel($event)"
                @onSelect="selectLevel()"
              ></custom-dropdown>
            </div>
          </div>

          <div class="row mb-3">
            <div class="col-3 text-l">
              <label class="text-l">Floor Activity:</label>
            </div>
            <div class="col-9">
                <!-- :default="'Edit floor activity'" -->
              <custom-dropdown
                :options="floors"
                :default="selectedFloor || 'Edit floor activity'"
                class="select"
                v-model="currentFloor"
                @onAdd="addFloor($event)"
                @onDelete="confirmDeleteFloor($event)"
                @onEdit="editFloor($event)"
              ></custom-dropdown>
            </div>
          </div>
          <div class="row text-l">
            <div class="col-3 col-md-3">
              <label for="settings-calc" class="text-l">Calculation</label>
            </div>
            <div class="col-8 col-md-9">
                <input type="text" 
                  required="required" 
                  autofocus="autofocus" 
                  class="settings-calc"
                  v-model="calculation"
                  v-on:change="editFloor"
                >
               
            </div>
         </div>
           <div class="row text-l">
             <div class="col-3 col-md-3">
              
            </div>
            <div class="col-8 col-md-9">
               <span class="text-small">Note: Please in sample format <strong>=Area/450/50*60. Area will be replace at proposal.</strong></span>
            </div>
         </div>

        <div class="grid-vertical mlr-auto box" v-if="showAddForm">
          <div class="grid-col">
            <div class="grid-title flex-row flex-100"> <h2>Add Floor</h2> </div>
            <div class="grid-form flex-row flex-95 col-md-12 pd-l-r-10">
              <div class="col-md-12 flex-row">
                <div class="col-md-2">Name: </div>
                <div class="col-md-10"><input type="text"  v-model="floorData.name"/> </div>
              </div>
              <div class="col-md-12 flex-row">
                <div class="col-md-2">Calculation: </div>
                <div class="col-md-10"><input type="text"  v-model="floorData.name"/> </div>
              </div>
              <div class="col-100 hidden">
                <div class="title flex-row flex-right"> Name: </div>
                <div class="form flex-row"> 
                  <input type="text"  v-model="floorData.name"/> 
                </div>
                <div class="title-2 flex-row flex-right"> Calculation: </div>
                <div class="form-2 flex-row"> 
                  <input type="text"  v-model="floorData.calculation"/> 
                </div>
                <div class=""> </div>
                <div class="form-3 flex-row"> 
                  <button type="button" class="btn-white btn-cancel flex-100 pd-t-b-5 v-space" @click="showAddForm = false">Cancel</button>
                  <button 
                    type="button" 
                    class="btn-white btn-save flex-100 pd-t-b-5"
                    @click="saveData"
                    >Save</button>  
                </div>
              </div>
            </div>
          </div>
        </div>
        
      </form>
    </div>
  </div>
</div>
  
</template>

<script>
import request from '@/helpers/fetchWrapper'
import { GET_TAXONOMY, UPDATE_TAXONOMY } from '@/utils/const'
import CustomDropdown from '../../components/common/CustomDropdown.vue';

import { mapGetters, mapState } from 'vuex'

export default {
  name: 'SettingsFloors',
  components: {
    CustomDropdown
  },

  data() {
    return {
      url:'floors',
      floors: [],
      floorData:{
        name:'',
        calculation:''
      },
      selected: null,
      levels:[],
      levelData: {
        name:'',
        description:''
      },
      action: 'add',
      current_id: 0,
      showAddForm: false,
      currentFloor: {
        name:'',
        acf:{calculation:''}
      },
      calculation:'',
    }
  },
  mounted() {
    this.fetchData();
    this.fetchLevels();
  },

  methods: {
    fetchLevels: function(){
        request(`${GET_TAXONOMY}levels`, {
          method: 'GET'
        }).then((res) => {  
          console.log(res)            
            this.levels = res
        }).catch((err) => {
          console.log(err)
        })
    },
    fetchData: function(){
        request(`${GET_TAXONOMY}floor_activities?per_page=99`, {
          method: 'GET'
        }).then((res) => {         
           this.floors = res 
        }).catch((err) => {
          console.log(err)
        })
    },
    toggleForm: function(action){
      this.action = action;
      this.showAddForm = !this.showAddForm
    },
    saveData: function() {
      const url = this.action==='add' ?  `${UPDATE_TAXONOMY}floor_activities`  : `${UPDATE_TAXONOMY}floor_activities/${this.currentFloor.id}` 

      let formData = {...this.floorData};
      request(`${url}`, {
        method: 'POST',
        body: JSON.stringify(formData),
        headers: {
          'Content-Type': 'application/json'      
        },
      }).then((res) => {
          console.log(res)
          this.floorData.name = ''
          this.floorData.calculation = ''
          this.showAddForm = false
          this.fetchData()
      }).catch((err) => {
        console.log(err)
      })
    },
    saveLevel: function() {
      const url = this.action==='add' ?  `${UPDATE_TAXONOMY}levels`  : `${UPDATE_TAXONOMY}levels/${this.id}`

      let formData = {...this.levelData};

      request(`${url}`, {
        method: 'POST',
        body: JSON.stringify(formData),
        headers: {
          'Content-Type': 'application/json'
        },
      }).then((res) => {
        console.log(res);        
        this.fetchLevels();
      }).catch((err) => {
        console.log(err)
      })
    },
    addLevel: function(e){
      this.action = 'add';
      this.levelData.name = e;
      const findRoom = this.levels.find(a => a.name === this.levelData.name)
      if (this.levelData.name !== '' && findRoom === undefined){
        this.saveLevel();
      }
      
    },
    editLevel: function(e = null){
      const name = e.value || this.selected.name;

      this.action = 'edit';
      this.levelData.name = name;
      this.id = e.id;

      this.saveLevel();
    },
    selectLevel: function(e = null){
      //const name = e.value || this.selected.name;

      this.action = 'select';
      this.levelData.description = 'selected';
      this.id = e.id;

      this.saveLevel();
    },
    addFloor: function(e){
      this.action = 'add';
      
      try {
        this.floorData.name = this.$refs.floor_select.$data.searchText;
      } catch (error) {
        this.floorData.name = e;
      }

      this.floorData.calculation = '';
      //if(this.floors.length ===0) return

      const findRoom = this.floors.find(a => a.name === this.floorData.name)
      if (this.floorData.name !== '' && findRoom === undefined){
        this.saveData()
      }
      
    },
    editFloor: function(e = null){
      const name = e.value || this.currentFloor.name;
      const calc = e.calculation || this.calculation;

      this.action = 'edit';
      this.floorData.name = name;
      this.floorData.calculation = calc;
      this.currentFloor.id = e.id || this.currentFloor.id;

      if (this.floorData.calculation !== '' && this.floorData.calculation !== undefined) {
        this.saveData();
      }
    },
    deleteFloor: function(id){
      // if (typeof this.currentFloor !== 'object' ) return ''
      
      request( `${UPDATE_TAXONOMY}floor_activities/${id}`, {
          method: 'DELETE'
        }).then((res) => {              
            console.log(res)
            this.fetchData()
        }).catch((err) => {
          console.log(err)
        })
    },
    deleteLevel: function(id){
      // if (typeof this.currentFloor !== 'object' ) return ''
      
      request( `${UPDATE_TAXONOMY}levels/${id}`, {
          method: 'DELETE'
        }).then((res) => {              
            console.log(res)
            this.fetchLevels()
        }).catch((err) => {
          console.log(err)
        })
    },
    confirmDeleteFloor: function(e) {

      const currentFloorId = e;
      const findFloor = this.floors.find(a => a.id === e);
      const currFloorName = findFloor.name;

      // if (this.currentFloor.id === null) return;

       this.$confirm(
        {
          message: `Are you sure you want to delete ${currFloorName}?`,
          button: {
            no: 'No',
            yes: 'Yes'
          },
          /**
          * Callback Function
          * @param {Boolean} confirm 
          */
          callback: confirm => {
            if (confirm) {
              this.deleteFloor(currentFloorId)
            }
          }
        }
      )
    },
    confirmDeleteLevel: function(e) {

      const currentLevelId = e;
      const findLevel = this.levels.find(a => a.id === e);
      const currLevelName = findLevel.name;

      // if (this.currentFloor.id === null) return;

       this.$confirm(
        {
          message: `Are you sure you want to delete ${currLevelName}?`,
          button: {
            no: 'No',
            yes: 'Yes'
          },
          /**
          * Callback Function
          * @param {Boolean} confirm 
          */
          callback: confirm => {
            if (confirm) {
              this.deleteLevel(currentLevelId)
            }
          }
        }
      )
    },

  },
  computed:{
    selectedFloor(){
      if (this.floors.length === null) return 'Edit floor activity';
      if (this.currentFloor.id==='Select') return ''
      console.log(this.currentFloor.acf)

      const item = this.floors.find(a=>a.id === this.currentFloor.id)
      if (item) return item.acf.calculation


      return ''
    },
    currentProposal () {
      return this.$store.state.currentProposal;
    },
    propoosal () {
      return this.$store.state.currentProposal;
    },
    globalLevels () {
      return this.$store.state.levels;
    },
    currentProposalLevel () {
      return this.$store.state.currentProposalLevel;
    },
    selectedLevel() {
       return this.currentProposalLevel?.level?.name || "Select level here"
    },
    ...mapGetters([
      'currentProposalLevelIndex',
      'currentProposalRoomIndex',

    ]),
    ...mapState([
      'floor_activities',

    ]),
  },
  
  watch: {
    currentFloor: function(val){

      
      if (this.floors.length=== null || typeof this.currentFloor !== 'object') return ''
      
      const item = this.floors.find(a=>a.id === val.id)
      if (item) {
        this.calculation = item.acf.calculation || this.selectedFloor
      }
    }
  }
};
</script>

<style lang="scss">
 
  .text-small {font-size: 10px;}
</style>