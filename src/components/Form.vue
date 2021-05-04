<template>
  <form id="form" @submit.prevent="sendDataToBack">
    <p>Please enter your name and pick the Sectors you are currently involved in.</p>
    <b-form-input id="name" v-model="name" placeholder="Enter your name"></b-form-input>
    <b-form-select id="select" multiple="" v-model="selected" size="sm">
      <custom-option
        v-for="sectorObj in sectors"
        v-bind:key="sectorObj.id"
        :value="sectorObj.id"
        :sector-obj=sectorObj
        @event="handler">
      </custom-option>
    </b-form-select>
    <b-form-checkbox
      id="checkbox-1"
      v-model="terms"
      name="checkbox-1"
      value="accepted"
      unchecked-value="not_accepted">
      Agree to terms
    </b-form-checkbox>
    <b-button id="submit" type="submit" value="Submit">Save</b-button>
    <p id="error">{{errorMsg}}</p>
  </form>
</template>

<script>
import axios from 'axios'
import CustomOption from './CustomOption'
export default {
  name: 'Home',
  components: { CustomOption },
  data () {
    return {
      errorMsg: '',
      selected: [],
      options: [],
      terms: false,
      name: null,
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
      if (this.name && this.terms && this.options !== 0) {
        this.inputIsCorrect()
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
      } else {
        this.error()
      }
    },
    putUserInput () {
      if (this.user_id && this.name && this.terms && this.options.length !== 0) {
        this.inputIsCorrect()
        console.log({
          user_id: this.user_id,
          name: this.name,
          sector_id: this.options,
          agreement: this.terms
        })
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
        this.error()
      }
    },
    handler (params) {
      this.saveOption(params)
    },
    saveOption (obj) {
      if (!this.options.includes(obj.id)) {
        this.options.push(obj.id)
      } else {
        const index = this.options.indexOf(obj.id)
        this.options.splice(index, 1)
      }
    },
    error () {
      this.errorMsg = 'Your input is incorrect!'
    },
    inputIsCorrect () {
      this.errorMsg = ''
    }
  },
  mounted () {
    this.getSectorsFromBack()
  }
}
</script>

<style>
  #name {
    margin: auto;
    width: 30%;
    justify-content: center;
  }
  #select {
    margin-top: 10px;
    margin-bottom: 10px;
    width: 30%;
    height: 250px;
    justify-content: center;
  }
  #submit {
    margin-top: 10px;
  }
  #form {
    margin-top: 50px;
  }
  #error {
    margin-top: 10px;
    color: red;
  }
</style>
