<template>
  <form id="form" @submit.prevent="sendDataToBack">
    <p>{{ sectors }}</p>
    <label for="name">Name:</label>
    <input type="text" id="name" name="name" v-model="name"><br>
    <label>Sectors:</label>
    <select multiple="" size="20">
      <option
        v-for="sectorObj in sectors"
        v-bind:key="sectorObj.id"
        :value="sectorObj.id"
        @click="saveOption(sectorObj)">
        {{formatName(sectorObj)}}{{ sectorObj['name'] }}
      </option>
    </select><br>
    <input type="checkbox" id="terms" name="terms" value="terms" v-model="terms">
    <label for="terms">Agree to terms</label><br>
    <input type="submit" value="Submit">
    <p>{{ this.options }}</p>
  </form>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Home',
  data () {
    return {
      options: [],
      terms: false,
      name: null,
      spacingMultiplier: 4,
      sectors: null,
      user_id: null
    }
  },
  methods: {
    getSectorsFromBack () {
      axios
        .get('http://localhost:8080/sectors')
        .then(response => (this.sectors = response.data))
        .catch(error => console.log(error))
    },
    sendDataToBack () {
      if (!this.user_id) {
        this.postUserInput()
      } else {
        this.putUserInput()
      }
    },
    postUserInput () {
      if (this.name && this.terms && this.options !== []) {
        axios
          .post('http://localhost:8080/user_input', {
            name: this.name,
            sector_id: this.options,
            agreement: this.terms
          })
          .then((response) => {
            this.user_id = response.data.user_id
            console.log(response)
          })
      }
    },
    putUserInput () {
      if (this.user_id && this.name && this.terms && this.options !== []) {
        axios
          .put('http://localhost:8080/user_input', {
            user_id: this.user_id,
            name: this.name,
            sector_id: this.options,
            agreement: this.terms
          })
          .then((response) => {
            console.log(response)
          })
      } else {
        console.log('we have some problem here')
      }
    },
    formatName (sectorObj) {
      return '\xa0'.repeat(this.spacingMultiplier * sectorObj.indent)
    },
    saveOption (obj) {
      if (!this.options.includes(obj.id)) {
        this.options.push(obj.id)
      } else {
        const index = this.options.indexOf(obj.id)
        this.options.splice(index, 1)
      }
    }
  },
  mounted () {
    this.getSectorsFromBack()
  }
}

</script>
