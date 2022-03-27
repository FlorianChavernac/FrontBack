<template>
  <div class="container mt-3">
    <select id="countrySelector" :@onChange="addcity()">
      <option
        v-for="country in data.countries"
        :key="country.id"
        :value="country._links.cities.href"
      >
        {{ country.name }}
      </option>
    </select>
    <table class="table table-bordered table-sm table-hover">
      <thead>
        <tr>
          <th>Nom</th>
          <th>population</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="city in data.cities" :key="city.id">
          <td>{{ city.name }}</td>
          <td>{{city.population}}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { reactive, onMounted } from "vue";

let data = reactive({
  countries: [],
  cities: [],
});

defineExpose({
  // On expose la méthode 'refresh' pour être utilisée par le parent
  refresh,
});

function refresh() {
  fetch("api/countries")
    .then((response) => {
      if (!response.ok) {
        // status != 2XX
        throw new Error(response.status);
      }
      return response.json();
    })
    .then((json) => {
      data.countries = json._embedded.countries;
    })
    .catch((error) => alert(error));
}

function addcity() {
  console.log("oui")
  let citiesHref = document.getElementById("countrySelector").value;
  console.log(citiesHref);
  fetch(citiesHref)
    .then((response) => {
      if (!response.ok) {
        // status != 2XX
        throw new Error(response.status);
      }
      return response.json();
    })
    .then((json) => {
      console.log(json);
      data.cities = json._embedded.cities;
    })
    .catch((error) => alert(error));
}

onMounted(refresh);
</script>

