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
        <th @click="sortEvents('price')">Price</th>
        <th @click="sortEvents('status')">Status</th>
        <th @click="sortEvents('timestamp')">Timestamp</th>
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
      filter: 'All',
      sorting: {
        price: false,
        status: false,
        timestamp: false
      }
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
    },
    sortEvents (sort) {
      switch (sort) {
        case 'price':
          this.sorting.timestamp = false
          this.sorting.status = false
          break
        case 'status':
          this.sorting.timestamp = false
          this.sorting.price = false
          break
        case 'timestamp':
          this.sorting.price = false
          this.sorting.status = false
          break
        default:
          break
      }
      if (this.sorting[sort]) {
        this.filteredEvents.sort( (a, b) => a[sort] > b[sort] ? 1 : -1 )
      } else {
        this.filteredEvents.sort( (a, b) => a[sort] < b[sort] ? 1 : -1 )
      }
      this.sorting[sort] = !this.sorting[sort]
    },
  },
  computed: {
    // filter the displayed events according to the filter select box
    // (dropdown element)
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
      // the event currently showing in the player box
      return this.filteredEvents[this.currentIdx]
    },
    statuses () {
      // extract all existing statuses from the events data, to display
      // in the select box
      return Array.from(new Set(this.events.map((event) => event.status)))
    }
  },
  mounted () {
    // convert prices to integers to help sorting then
    // having some missing leading zeros in some of the events.
    // This modifies the actual data we have received
    this.events.forEach( (event) => {
      event.price = parseInt(event.price)
    })
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