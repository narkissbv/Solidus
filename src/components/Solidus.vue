<template>
  <div>
    <div class="player">
      <div class="">
        <table>
          <tr>
            <th>Price</th>
            <th>Status</th>
            <th>Timestamp</th>
          </tr>
          <Event :event="currentEvent"/>
        </table>
      </div>
      <div class="">
        <button @click="player('start')"><img src="../assets/skip-backward.svg"/></button>
        <button @click="player(-1)"><img src="../assets/skip-previous.svg"/></button>
        <button><img src="../assets/play.svg"/></button>
        <button @click="player(1)"><img src="../assets/skip-next.svg"/></button>
        <button @click="player('end')"><img src="../assets/skip-forward.svg"/></button>
      </div>
    </div>
    <div class="filter">
      <label>Filter by </label>
      <select v-model="filter">
        <option>All</option>
        <option v-for="(status, idx) in statuses"
                :key="idx">
          {{ status }}
        </option>
      </select>
    </div>
    <div>
      Events table
    </div>
    <table>
      <tr>
        <th>Price</th>
        <th>Status</th>
        <th>Timestamp</th>
      </tr>
      <Event v-for="(event, idx) in filteredEvents" :key="idx"
             :event="{...event, idx}"
             @selection="selectCurrent(idx)"/>
    </table>


    
  </div>
</template>

<script>
import events from '@/data/events.json'
import Event from '@/components/Event'
export default {
  data () {
    return {
      events,
      currentIdx: 0,
      eventsDisplay: [],
      filter: 'All'
    }
  },
  components: {
    Event,
  },
  methods: {
    player(step) {
      switch (step) {
        case 'start':
          this.currentIdx = 0
          break
        case 'end':
          this.currentIdx = this.filteredEvents.length - 1
          break
        default:
          this.currentIdx+=step
          if (step > 0) {
            if (this.currentIdx > this.filteredEvents.length - 1) {
              this.currentEvent = this.filteredEvents[this.filteredEvents.length - 1]
              this.currentIdx = this.filteredEvents.length - 1
            }
          } else {
            if (this.currentIdx < 0) {
              this.currentIdx = 0
            }
          }
          break
      }
    },
    selectCurrent(idx) {
      this.currentIdx = idx
    }
  },
  computed: {
    filteredEvents () {
      if (this.filter === 'All') {
        return this.events
      } else {
        return events.filter( (event) => {
          return event.status === this.filter
        })
      }
    },
    currentEvent () {
      return this.filteredEvents[this.currentIdx]
    },
    statuses () {
      return Array.from(new Set(this.events.map((event) => event.status)))
    }
  }
}
</script>

<style>
  table {
    cursor: pointer
  }
  table tr:hover td {
    background: lightgray
  }

  .player,
  .filter {
    margin-bottom: 30px;
  }
</style>