<template>
<div class='text-center'>
  <div v-if='!sidebarActive' class='h-100 pt-2' @click='toggleSideBar(!sidebarActive)'>
    <img class='icon' src='../assets/right-arrow.svg' alt='Right Arrow' title='Open Sidebar' >
    <!-- <img class='icon my-4' src='../assets/add.svg' alt='Cross' title='Add Task' @click='addTask'> -->
  </div>

  <div v-else class='text-left'>

    <div class='divTop borderBtm'>
      <div class='ml-3 pt-2'>
        <h5 class='d-inline'>Task Organizer</h5>
        <img class='icon mr-3 float-right' src='../assets/left-arrow.svg' alt='left-arrow' title='Close Sidebar' @click='toggleSideBar(!sidebarActive)'>
      </div>
    </div>

    <div class='userDiv borderBtm'>
      <div v-if='user'>
        <p class='my-0 ml-3 py-1 font-weight-bold'>Profile</p>
        <p class='my-0 ml-3 py-2'>{{ user.username }}</p>
        <div class='hoverDiv py-2' @click='logout'>
          <img class='textIcon d-inline ml-3' src='../assets/sidebar/sign.svg' alt='Sign out' title='Sign out'>
          <p class='my-0 text-primary d-inline ml-2 text'>Logout</p>
        </div>
      </div>
      <div v-else>
        <div class='hoverDiv py-2' @click='login'>
          <img class='textIcon d-inline ml-3' src='../assets/sidebar/sign.svg' alt='Sign out' title='Sign out'>
          <p class='my-0 text-primary d-inline ml-2 text'>Login</p>
        </div>
      </div>
    </div>

    <div v-if='user'>
      <p class='my-0 ml-3 py-2 font-weight-bold'>Features</p>

      <div class='hoverDiv py-2' @click="addTask('')">
        <img class='textIcon d-inline ml-3' src='../assets/sidebar/add.svg' alt='Add' title='Add'>
        <p class='my-0 text-primary d-inline ml-2 text'>Add Task</p>
      </div>

      <div class='hoverDiv py-2' :class="{ dropdown: sidebarDisplay.fromDate }" @click='sidebarDisplay.fromDate = !sidebarDisplay.fromDate'>
        <img class='textIcon d-inline ml-3' src='../assets/sidebar/calendar.svg' alt='Calendar' title='Calendar'>
        <p class='my-0 text-primary d-inline ml-2 text'>From Date</p>

        <img v-if='!sidebarDisplay.fromDate' class='angle mr-2 d-inline' src='../assets/sidebar/angle-right.svg'>
        <img v-else class='angle mr-2 d-inline' src='../assets/sidebar/angle-down.svg'>

        <div v-if='sidebarDisplay.fromDate' class='py-2 dropdown'>
          <input class='ml-3 form-control w-75'
                 :value='dateSelected'
                 type='date'
                 @change='updateDateSelected'
                 @click.stop='stopTheEvent'
          >
        </div>
      </div>

      <div class='hoverDiv py-2' :class="{ dropdown: sidebarDisplay.selectTask }" @click='sidebarDisplay.selectTask = !sidebarDisplay.selectTask'>
        <img class='textIcon d-inline ml-3' src='../assets/sidebar/thumbtack.svg' alt='Thumbtack' title='Thumbtack'>
        <p class='my-0 text-primary d-inline ml-2 text'>Select Task</p>

        <img v-if='!sidebarDisplay.selectTask' class='angle mr-2 d-inline' src='../assets/sidebar/angle-right.svg'>
        <img v-else class='angle mr-2 d-inline' src='../assets/sidebar/angle-down.svg'>

        <div v-if='sidebarDisplay.selectTask' class='py-2 dropdown'>
          <select class='ml-3 form-control w-75' :value='taskSelected' @change='updateTaskSelected' @click.stop='stopTheEvent'>
            <option value=''></option>
            <option v-for='rootid in roots' :key='rootid' :value='rootid'>{{ tasks[rootid].task }}</option>
          </select>
        </div>
      </div>
      <!--
      <div class='hoverDiv py-2' :class="{ dropdown: sidebarDisplay.taskStatus }" @click='sidebarDisplay.taskStatus = !sidebarDisplay.taskStatus'>
        <img class='textIcon d-inline ml-3' src='../assets/sidebar/status.svg' alt='Status' title='Status'>
        <p class='my-0 text-primary d-inline ml-2 text'>Task Status</p>

        <img v-if='!sidebarDisplay.taskStatus' class='angle mr-2 d-inline' src='../assets/sidebar/angle-right.svg'>
        <img v-else class='angle mr-2 d-inline' src='../assets/sidebar/angle-down.svg'>

        <div v-if='sidebarDisplay.taskStatus' class='py-2 dropdown'>
          <select class='ml-3 form-control w-75' v-model='taskstatus' @click.stop='stopTheEvent'>
            <option value=''></option>
            <option value='Active'>Active</option>
            <option value='Completed'>Completed</option>
            <option value='Incompleted'>Incompleted</option>
          </select>
        </div>
      </div>

      <div class='hoverDiv py-2' :class="{ dropdown: sidebarDisplay.childrenDisplayed }" @click='sidebarDisplay.childrenDisplayed = !sidebarDisplay.childrenDisplayed'>
        <img class='textIcon d-inline ml-3' src='../assets/sidebar/depth.svg' alt='Depth' title='Depth'>
        <p class='my-0 text-primary d-inline ml-2 text'>Children Displayed</p>

        <img v-if='!sidebarDisplay.childrenDisplayed' class='angle mr-2 d-inline' src='../assets/sidebar/angle-right.svg'>
        <img v-else class='angle mr-2 d-inline' src='../assets/sidebar/angle-down.svg'>

        <div v-if='sidebarDisplay.childrenDisplayed' class='py-2 dropdown'>
          <select class='ml-3 form-control w-75' v-model='childrendisplayed' @click.stop='stopTheEvent'>
            <option value='0'>0</option>
            <option value='1'>1</option>
            <option value='2'>2</option>
            <option value='3'>3</option>
            <option value='4'>4</option>
            <option value='5'>5</option>
          </select>
        </div>
      </div>
      -->
    </div>
  </div>
</div>
</template>


<script>
import { currentDate } from '../js/sharedFunctions.js';
import { mapState, mapActions, mapMutations } from 'vuex'

export default {
  name: 'Sidebar',
  data: function() {
    return {
      sidebarDisplay: {
        fromDate: false,
        selectTask: false,
        taskStatus: false,
        childrenDisplayed: false
      }
    };
  },
  computed: {
    ...mapState([
      'user'
    ]),
    ...mapState('tasks', [
      'roots',
      'tasks'
    ]),
    ...mapState('sidebar', [
      'dateSelected',
      'taskSelected',
      'sidebarActive'
    ])
  },
  methods: {
    ...mapMutations('sidebar', {
      toggleSideBar: 'SET_SIDEBAR_ACTIVE'
    }),
    ...mapActions('sidebar', [
      'updateTaskSelected',
      'updateDateSelected'
    ]),
    ...mapActions('display', [
      'addTask',
      'login'
    ]),
    ...mapActions([
      'logout'
    ]),
    currentDate,
    stopTheEvent (event) {
      event.stopPropagation();
    }
  },
  mounted: function() {
    this.$store.dispatch('sidebar/updateDateSelected', null);
  }
}
</script>


<style scoped>
.icon {
  display: block;
  margin-left: auto;
  margin-right: auto;
  cursor: pointer;
  height: 26px;
}

.borderBtm {
  border-bottom: 1px solid black;
}

.divTop {
  height: 42px;
}

.angle {
  height: 24px;
  float: right;
  vertical-align: middle;
}

.hoverDiv:hover {
  background-color: #e3e3e3;
  cursor: pointer
}

.dropdown {
  background-color: #e3e3e3;
}

.text, .textIcon {
  vertical-align: middle;
}

.textIcon {
  height: 18px;
  width: 18px;
}
</style>
