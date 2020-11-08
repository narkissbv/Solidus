<template>
  <tr @click="selectEvent">
    <td> {{ event.price }} </td>
    <td> {{ event.status }} </td>
    <td> {{ event.timestamp }} </td>
  </tr>
</template>

<script>
export default {
  props: {
    event
  },
  methods: {
    selectEvent() {
      this.$emit('selection')
    }
  }
}
</script>