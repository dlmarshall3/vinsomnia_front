<template>

<div id="vinGarage" class="container-fluid">
  <hr>
  <h3 class="subtitle is-1">Your Garage</h3>
  
  <div id="instructions">
    <p class="title is-3 instructions">Instructions:</p>
    <ul>
      <li><b>To add a vehicle:</b>
      <br>Insert the year, make, model, and color in the form to the right. Once you hit submit, the database will be updated.</li>
      <li><b>To delete a vehicle:</b>
      <br>Click the trashcan to the right of the vehicle you'd like to delete, and it'll be removed from the database.</li>
      <li><b>To update a vehicle:</b>
      <br>Click the "Reveal IDs" button to see the ID of the vehicle you want to edit. Once you fill in all of the fields, click "Update Existing," and you're good to go!</li>
    </ul>
  </div>

  <div class="columns is-two-thirds">
    
    <div class="column" id="garageData">
      <vinTable :vehicles="apiData" :isShow="isShow"></vinTable>
    </div>
    
    <div class="column is-one-third">
      
      <form @submit.prevent id="vinForm" ref="formInput">
      
      <b-field>
        <b-input placeholder="Vehicle ID"
        type="number"
        icon-pack="fas"
        icon="id-card"
        v-model="id">
        </b-input>
      </b-field>

      <b-field>
        <b-input placeholder="Year"
        type="number"
        icon-pack="fas"
        icon="hashtag"
        v-model="year" required>
        </b-input>
      </b-field>

      <b-field>
        <b-input placeholder="Make"
        type="text"
        icon-pack="fas"
        icon="car"
        v-model="make" required>
        </b-input>
      </b-field>

      <b-field>
        <b-input placeholder="Model"
        type="text"
        icon-pack="fas"
        icon="truck-pickup"
        v-model="model" required>
        </b-input>
      </b-field>

      <b-field>
        <b-input placeholder="Color"
        type="text"
        icon-pack="fas"
        icon="palette"
        v-model="new_color" required>
        </b-input>

      </b-field>

      <div class="column" id="vinButtons">
      <b-button type="is-success" v-on:click="addVehicle(); resetInputs()" class="vinButton">Submit</b-button>
      <b-button type="is-warning" v-on:click="isShow = !isShow" class="vinButton">Reveal IDs</b-button>
      <b-button type="is-link" v-on:click="updateVehicle(id); resetInputs()" class="vinButton">Update Existing</b-button>
      </div>
      
      </form>
    </div>
  </div>
</div>  

    
    
    


</template>

<script>

import vinTable from './vinTable.vue'

export default {
  name: 'vinGarage',
  components: {
      vinTable,
  },

  methods: {

    //add vehicle to the db
    addVehicle(){
      const requestOptions = {
        method: 'POST',
        headers: { 'Content-Type': 'application/json'},
        body: JSON.stringify({
          id: this.id,
          year: this.year,
          make: this.make,
          model: this.model,
          new_color: this.new_color
        })
      }
      //with having the id field for updating the vehicle, needed to make sure that could be left blank and still add a new vehicle to the db.
      //turned into object to access values
      let inputs = JSON.parse(requestOptions.body)
      if(inputs.year == "" || inputs.make == "" || inputs.model == "" || inputs.new_color == ""){
        alert('Missing fields')
      }
      else {
      fetch('//carmigo-master.herokuapp.com/api/v1/vehicles/', requestOptions)
      .then(alert('Vehicle added'))
      .then(function setTimeout() { location.reload(), 200})
      }
    },

    //shows the vehicle when components are mounted
    showVehicles(){
      const data = fetch('//carmigo-master.herokuapp.com/api/v1/vehicles/')
      .then((response) => response.json())
      .then((responseData) => (this.apiData = responseData));
      this.apiData = data;
    },

    //update vehicle in database as long as all fields are filled out
    updateVehicle(){
      const requestOptions = {
        method: 'PUT',
        headers: { 'Content-Type': 'application/json'},
        body: JSON.stringify({
          id: this.id,
          year: this.year,
          make: this.make,
          model: this.model,
          new_color: this.new_color
        })
      }
      let inputs = JSON.parse(requestOptions.body)
      if(Object.values(inputs).some(val => val === "")){
        alert('Missing field')
      }
      else {
      fetch(`//carmigo-master.herokuapp.com/api/v1/vehicles/${this.id}`, requestOptions)
      .then(alert('Vehicle updated'))
      .then(function setTimeout() { location.reload(), 200})
      }
    },

    resetInputs(){
      this.$refs["formInput"].reset();
    }
  },

  data(){
    return {
      apiData: "",
      id: "",
      year: "",
      make: "",
      model: "",
      new_color: "",
      isShow: false
    }

  },
  mounted(){
    this.showVehicles()
  }
}
</script>

<style>

#vinGarage {
    text-align: center;
    padding-top: 20px;
    padding-bottom: 20px;
    background-color: rgba(255,255,255,.45);
}

#vinForm{
  display: flex;
  align-items: center;
  flex-direction: column;
  align-content: space-evenly;
}

#garageData{
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}

#instructions {
  /* margin-bottom: 50px; */
  line-height: 2;
}

.vinButton {
  margin: 10px;
}
 

</style>