<template>
  <div class="footer" v-if="$route.name!='proposals'" v-bind:class="{ 'hide-footer': $route.path == '/proposals' }">
    <div class="tabs">
<!--       <router-link class="" :to="{ name: 'ProposalHeader' }">Header</router-link> -->
      <router-link class="" :to="{ name: 'ProposalLevels' }">Levels</router-link>
      <router-link class="" :to="{ name: 'ProposalRooms' }">Rooms</router-link>
      <router-link class="" :to="{ name: 'ProposalActivities' }">Activities</router-link>
      <router-link class="" :to="{ name: 'ProposaPeriodicals' }">Periodicals</router-link>
      <router-link class="" :to="{ name: 'ProposalOptional' }">Optional</router-link>
 
    </div>
  </div>
</template>

<script>
export default {
  name: 'Proposalfooter',

  data() {
    return {
      
    };
  },

  mounted() {
    
  },

  methods: {
    
  },
};
</script>

<style lang="scss" scoped>
  .footer {
    height: 70px;
    width: 100vw;
    margin-top: 50px;
    box-sizing: border-box;
    background: $footer-color;
    bottom: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    position: absolute;
    .tab-flex {
      display: flex;
    }
    .tabs {
      display: flex;
      justify-content: space-between;
      align-content: center;      
      padding: 0 20px;
      color: $white;
      background: $footer-color;  
      a {
        color: #fff;
        text-decoration: none;
        color: white;
         font-size: 14px;
        text-decoration: none;
        font-weight: bold;
        width: auto;
      }
      .router-link-active {      
        color: #C1D72E !important;
      }


    }
    .btn {
      background-color: $footer-color;
    }
    &.hide-footer {
      display: none !important;
    }

  }
    @media screen and (max-width: 649px) and (min-width: 1px) {      
      .footer {
        .tabs {
          a {
            font-size:14px; 
          }
        }
      }
    }
</style>