<template>
  <div id="ProposalHeader" class="header m-0">
  <div class="container">
  <div class="row py-2">
    <div class="icons float-l col-6">
      <router-link to="/proposals" title="home"
      class="top-link"><img src="@/assets/icons/0.75x/home-white.png" class="icon home"/></router-link>

      <router-link to="/settings" title="settings" v-if="adminOnly"
      class="top-link"><img src="@/assets/icons/0.75x/settings-white.png" class="icon settings"/></router-link>

      <button class="btn edit-btn top-link m-0" title="edit">
        <img src="@/assets/icons/0.75x/download-icon.png" class="icon download"/>
      </button>
      <button class="btn edit-btn top-link m-0" title="edit" @click="openNewProposal">
        <img src="@/assets/icons/0.75x/add-icon-white.png" class="icon add"/>
      </button>
    </div>
    <div class="icon center col-1 col-md-2"></div>
      <div class="icons col-4">
        <div class="icons d-block" v-if="showDuplicateRemove" v-bind:class="{ 'hide-icons': $route.path == '/proposals' }">
          
          <button class="btn remove-btn top-link float-right" title="remove"><img src="@/assets/icons/0.75x/delete-icon.png" class="icon remove" @click="confirmDeleteProposal"/></button>
          <button class="btn duplicate-btn top-link float-right" title="duplicate"  
            @click="confirmDuplicateProposal">
            <img src="@/assets/icons/0.75x/duplicate-icon.png" class="icon duplicate" />
          </button>
        </div>
      </div>
    </div>
  </div>


  </div>
</template>

<script>

import { mapGetters, mapState } from 'vuex'

export default {
  name: 'Header',

  data() {
    return {
      
    };
  },

  mounted() {
    
  },

  methods: {
    openNewProposal(){
      this.$store.dispatch({ type: 'createNewProposal'}).then(res => {
        if (res){
          this.$router.push({ name: 'ProposalHeader' })
        }
      })
    },
    confirmDeleteProposal: function(e) {

      let title = this.proposal.title.rendered; 

       this.$confirm(
        {
          message: `Are you sure you want to delete ${title}?`,
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
              this.deleteProposal(e);
            }
          }
        }
      )
    },

    deleteProposal: function(e){
      this.$store.dispatch('deleteProposal').then(res => {
        if (res){
           this.$router.push({ name: 'ProposalMain' })
        }
      }).catch(e => {
          console.log(e, "error encountered")
      });
    },
    confirmDuplicateProposal: function(e) {

      let title = this.proposal.title.rendered; 

       this.$confirm(
        {
          message: `Are you sure you want to duplicate ${title}?`,
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
              this.duplicateProposal(e);
            }
          }
        }
      )
    },

    duplicateProposal: function(e){
      this.$store.dispatch('duplicateProposal').then(res => {
        if (res){
           this.$router.push({ name: 'ProposalMain' })
        }
      }).catch(e => {
          console.log(e, "error encountered")
      });
    }
    
  },
  computed: {
     score() { return this.$store.state.count },
     showAddNew() {
       return this.$route.name === 'ProposalMain' || this.$route.name ==='Proposals'
     },
     showDuplicateRemove() {
      return this.$store.state.currentProposal === null ? false : true;
     },
     proposal() {
      return this.$store.state.currentProposal;
     },
     adminOnly() {
       const userData = localStorage.getItem('user')
       const userRoles = JSON.parse(userData).roles
       console.log(userRoles.includes("administrator"), 'admin?')
       return userRoles.includes("administrator")
     }
  }
};
</script>

<style lang="scss">
  #ProposalHeader{
  .top-link {
    width: 40px;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .d-block {
    justify-content: end;
  }
  .header {
    background: $primary-color;
    min-height: 50px;
    display: flex;
    align-content: center;
    flex-wrap: column wrap;
    padding:10 0;
    .icon {
      width: 100%;
      height: auto;
      margin: 0;
      .setting, .home {
        width: 30px;
      }
    }
    .icons {
      display: flex;
      flex-wrap: row wrap;
      // float: left;
    }
    .icons > * {
      flex: 1 1 100%;
    }
    .left {
      padding-left: 0;
      display: inherit;
      width: 30%;
      justify-content: space-evenly;
    }
    .center {
      display: inherit;
      width: 30%;
    }
    .right {
      width: 80%;
      position: relative;
      display: inherit;
      justify-content: space-evenly;
      float: right;
      padding-right: 73px;
    }
    .btn {
      background: none;
      border: none;
      margin: 0 !important;
      &:hover {
        opacity: .8;
        background-color: adjust-hue($color: #000, $degrees: 0);
      }
    }
    }

    @media screen and (max-width: 649px) and (min-width: 1px) {      
      .center {
        width:10%;
      }    
      .top-link {
        width: 40px;
      }
    }
  }

</style>