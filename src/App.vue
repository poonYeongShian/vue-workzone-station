<script setup>
import { ref } from 'vue'

// Existing station in db
const stations = ref([
  { auto_no: uuid(), station_name: "Foyer Ambass", description: "Foyer Ambasador on 1st", staff_req: 5, station_name_before_edit:"", description_before_edit:"", staff_req_before_edit: 0, enable: true, editing: false },
  { auto_no: uuid(), station_name: "Coffer Station", description: "-", staff_req: 12, station_name_before_edit:"", description_before_edit:"", staff_req_before_edit: 0, enable: false, editing: false},
  { auto_no: uuid(), station_name: "Ticket Kiosk", description: "All kiosk", staff_req: 7, station_name_before_edit:"", description_before_edit:"", staff_req_before_edit: 0, enable: false, editing: false }
])

// New station to be added
const new_stations = ref([])

//generate id
function uuid() {
    let uuid = "";
    for (let i = 0; i < 32; i++) {
        let random = (Math.random() * 16) | 0;

        if (i === 8 || i === 12 || i === 16 || i === 20)
            uuid += "-";

        uuid += (i === 12 ? 4 : i === 16 ? (random & 3) | 8 : random).toString(16);
    }
    return uuid;
}

// Add new station to new_stations list
const addNewStation = (station_name, description, staff_req, enable, editing) => {
    new_stations.value.push({auto_no: uuid(), station_name: station_name, description: description, staff_req: staff_req, station_name_before_edit:"", description_before_edit:"", staff_req_before_edit: 0, enable: enable, editing: editing})
}

// Insert new station into db
function addNewStationToDB(station) {
    deleteStationFromNewList(station);
    stations.value.push(station);
}

// Delete stations list
function deleteStationFromNewList(station) {
    new_stations.value = new_stations.value.filter((t) => t.auto_no !== station.auto_no);
}

// Delete station from DB
function deleteStationFromDB(station) {
    stations.value = stations.value.filter((t) => t.auto_no !== station.auto_no);
}

// Toggle button update state
function toggleCheckBox(station){
    station.enable = !station.enable;
}

// Update text field
function updateStation(station, $event, attribute){
    switch (attribute) {
    case "name":
      station.station_name= $event.target.value;
      break;
    case "desc":
      station.description = $event.target.value;
      break;
    case "req":
      station.staff_req = $event.target.value;
      break;
    default:
      // Error msg here
      console.log("Invalid input attribute, pick one of the following string attribute [name, desc, req]");
  }
}

// editing toggle
function editToggle(station){
    station.editing = !station.editing;
}

// backup filed before edit
function backUpData(station){
    station.station_name_before_edit = station.station_name;
    station.description_before_edit = station.description;
    station.staff_req_before_edit = station.staff_req;
}

// restore field to moment before edit
function restoreData(station){
  station.station_name = station.station_name_before_edit;
  station.description = station.description_before_edit;
  station.staff_req = station.staff_req_before_edit;
}

// update change
function updateChange(station){
    console.log("Update Change");
}


</script>

<style>
.label-wrapper {
  margin: 0;
  flex: 0 0 100%;
  text-align: center;
}

[class*="__lg"] {
  display: inline-block;
  width: 100%;
  font-size: 1.9rem;
}

[class*="__lg"]:not(:last-child) {
  margin-bottom: 1rem;
}

@media screen and (min-width: 620px) {
  [class*="__lg"] {
    font-size: 2.4rem;
  }
}

.visually-hidden {
  position: absolute;
  height: 1px;
  width: 1px;
  overflow: hidden;
  clip: rect(1px 1px 1px 1px);
  clip: rect(1px, 1px, 1px, 1px);
  clip-path: rect(1px, 1px, 1px, 1px);
  white-space: nowrap;
}

[class*="stack"]>* {
  margin-top: 0;
  margin-bottom: 0;
}

.stack-small>*+* {
  margin-top: 1.25rem;
}

.stack-large>*+* {
  margin-top: 2.5rem;
}

@media screen and (min-width: 550px) {
  .stack-small>*+* {
    margin-top: 1.4rem;
  }

  .stack-large>*+* {
    margin-top: 2.8rem;
  }
}

/* End global styles */
#app {
  background: #fff;
  margin: 2rem 0 4rem 0;
  padding: 1rem;
  padding-top: 0;
  position: relative;
  box-shadow:
    0 2px 4px 0 rgb(0 0 0 / 20%),
    0 2.5rem 5rem 0 rgb(0 0 0 / 10%);
}

@media screen and (min-width: 550px) {
  #app {
    padding: 4rem;
  }
}

#app>* {
  max-width: 50rem;
  margin-left: auto;
  margin-right: auto;
  font-size: 10pt;
}

#app>form {
  max-width: 100%;
}

#app h1 {
  display: block;
  min-width: 100%;
  width: 100%;
  text-align: center;
  margin: 0;
  margin-bottom: 1rem;
}



/* table css */
.table {
  width: 100%;
  border-collapse: collapse;
  border-spacing: 0;
}

.table th,
.table td {
  border: 1px solid #dee2e6;
  padding: 8px;
}

.table th {
  background-color: #f8f9fa;
  text-align: left;
}

/* Style the form switch */
.form-switch {
  display: inline-flex;
  align-items: center;
}

.form-switch input[type="checkbox"] {
  margin-right: 5px;
}


</style>

<template>
  <div class="container">
    <!-- Add new row button -->
    <div class="row justify-content-end">
      <div class="col-1">
        <button @click="addNewStation('station', 'station description', 0, true, false)" type="button" class="btn btn-primary">
          <i class="bi bi-plus"></i>
        </button>
      </div>
    </div>

    <!-- Add new station table -->
    <table class="table">
      <tbody>
        <tr v-for="(station, index) in new_stations" :key="station.auto_no">
          <td>
            <input :value="station.station_name" @input="updateStation(station, $event, 'name')" class="form-control">
          </td>
          <td>
            <input :value="station.description" @input="updateStation(station, $event, 'desc')" class="form-control">
          </td>
          <td>
            <input :value="station.staff_req" @input="updateStation(station, $event, 'req')" class="form-control" type="number" min="0">
          </td>
          <td>
            <!-- Toggle check box -->
            <div class="form-check form-switch">
              <input @change="toggleCheckBox(station)" class="form-check-input" type="checkbox" role="switch" :checked="station.enable">
            </div>
          </td>
          <td>
            <!-- Add -->
            <button @click="addNewStationToDB(station)" type="button" class="btn btn-success">
              <i class="bi bi-check"></i>
            </button>
          </td>
          <td>
            <!-- Delete -->
            <button @click="deleteStationFromNewList(station)" type="button" class="btn btn-danger">
              <i class="bi bi-x"></i>
            </button>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Edit exist stations table -->
    <table class="table">
      <tbody>
        <tr v-for="(station, index) in stations" :key="station.auto_no">
          <td>
            <input :value="station.station_name" @input="updateStation(station, $event, 'name')" class="form-control" :class="{'form-control-plaintext': !station.editing}" :readonly="!station.editing">
          </td>
          <td>
            <input :value="station.description" @input="updateStation(station, $event, 'desc')" class="form-control" :class="{'form-control-plaintext': !station.editing}" :readonly="!station.editing">
          </td>
          <td>
            <input :value="station.staff_req" @input="updateStation(station, $event, 'req')" class="form-control" :class="{'form-control-plaintext': !station.editing}" :readonly="!station.editing" type="number" min="0">
          </td>
          <td>
            <!-- Toggle button -->
            <div class="form-check form-switch">
              <input class="form-check-input" type="checkbox" role="switch" :checked="station.enable">
            </div>
          </td>
          <td>
            <!-- Edit button -->
            <button @click="editToggle(station); backUpData(station)" type="button" class="btn btn-primary" v-if="!station.editing">
              <i class="bi bi-pen"></i>
            </button>
            <!-- Confirm edit change button -->
            <button @click="editToggle(station); updateChange(station)" type="button" class="btn btn-success" v-else>
              <i class="bi bi-check"></i>
            </button>
          </td>
          <td>
            <!-- Delete button -->
            <button @click="deleteStationFromDB(station)" type="button" class="btn btn-primary" v-if="!station.editing">
              <i class="bi bi-trash"></i>
            </button>
             <!-- Cancel edit change button -->
            <button @click="editToggle(station); restoreData(station)" type="button" class="btn btn-danger" v-else>
              <i class="bi bi-x"></i>
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>