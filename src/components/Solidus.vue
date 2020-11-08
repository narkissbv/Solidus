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
    <table class="events-table">
      <tr>
        <th @click="sortEvents('price')">
          Price
          <img src="../assets/chevron-down.svg"
               :class="{
                 'reverse' : sorting.price < 0,
                 'show' : sorting.price
               }"
          />
        </th>
        <th @click="sortEvents('status')">
          Status
          <img src="../assets/chevron-down.svg"
               :class="{
                 'reverse' : sorting.status < 0,
                 'show' : sorting.status
               }"
          />
        </th>
        <th @click="sortEvents('timestamp')">
          Timestamp
          <img src="../assets/chevron-down.svg"
               :class="{
                 'reverse' : sorting.timestamp < 0,
                 'show' : sorting.timestamp
               }"
          />

        </th>
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
        price: 0,
        status: 0,
        timestamp: 1
      }
    }
  },
  components: {
    Event,
  },
  methods: {
    player(step) {
      // evaluate the commands pressed on the player's action buttons
      // accepts an integer (positive to more forward, and negative to move backwards)
      // or the keywords 'start' and 'end' to skip all the way to the beginning / end.
      // assumes input is valid. No validation mechanism (something that should be
      // included in unit tests)
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
      // set the currently displayed event in the player box to a specific event
      // clicked on in the events table
      this.currentIdx = idx
    },
    sortEvents (sort) {
      // reset all other sorting variables (we are only sorting according to one column)
      switch (sort) {
        case 'price':
          this.sorting.timestamp = 0
          this.sorting.status = 0
          break
        case 'status':
          this.sorting.timestamp = 0
          this.sorting.price = 0
          break
        case 'timestamp':
          this.sorting.price = 0
          this.sorting.status = 0
          break
        default:
          break
      }
      if (this.sorting[sort] === 0) {
        this.sorting[sort] = 1
      }
      if (this.sorting[sort] > 0) {
        this.filteredEvents.sort( (a, b) => a[sort] > b[sort] ? 1 : -1 )
      } else {
        this.filteredEvents.sort( (a, b) => a[sort] < b[sort] ? 1 : -1 )
      }
      this.sorting[sort] *= -1
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
    this.sortEvents('timestamp')
  }
}
</script>

<style>
  table.events-table {
    cursor: pointer
  }
  table.events-table th {
    font-size: 24px;
    border-bottom: 2px solid gray
  }
  table.events-table th img {
    display: none;
  }
  table.events-table tr:hover td {
    background: lightgray
  }
  table.events-table th img.show {
    display: inline-block;
  }
  table.events-table th img.reverse {
    transform: rotate(180deg);
    transition: all 0.3s ease;
  }

  .player,
  .filter {
    margin-bottom: 40px;
  }

</style>