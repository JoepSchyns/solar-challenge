<template>
  <table style="width:100%">
  <tr>
    <th>Resultat aktuelle Runde</th>
    <td>{{ tabelle_x }}</td>
  </tr>
  <tr style="text-align:left">
    <th>Rangliste<br>
          <div class="btn-group-vertical">
            <button type="button" class="btn btn-outline-primary">Vorlauf 1</button>
            <button type="button" class="btn btn-outline-secondary">Vorlauf 2</button>
            <button type="button" class="btn btn-outline-success">Viertel Finale</button>
            <button type="button" class="btn btn-outline-danger">Halbe finale</button>
            <button type="button" class="btn btn-outline-warning">Finale</button>
          </div>
    </th>
    <td>{{ tabelle_y }}</td>
  </tr>
</table>
</template>

<script>
export default {
  data() {
    return {
      tabelle_x: 'TABELLE x',
      tabelle_y: 'TABELLE y',
      data: null, // parsed csv data
      error: null, // error state
      timeout: null, // timeout for data lookup
    }
  },
  beforeMount(){
    this.getData(); // Init data lookup
  },
  methods: {
   async getData(){
      try{
        clearTimeout(this.timeout); // Prevent parallel data lookups
        const result = await fetch('time.csv'); // Request data from 'server'
        const conversion = require('csvtojson');
        this.data = conversion(result.data); // Parse data and make available to Vue with this.data
        this.timeout = setTimeout(this.getData, document.hidden ? 5000 : 1000); 
        // Repeat getData function after 1 second or 5 seconds if the browser tab is not visible
      } catch(e){ // If something did not go as planned
        this.data = null; // Remove previous data
        console.error(e); // Print error in console
        this.error = e.toString(); // Make error available for displaying in Vue with this.error
        this.timeout = setTimeout(this.getData, 5000); // Repeat getData function after 5 seconds
      }
    }       
  },
  unmounted(){
    clearTimeout(this.timeout); // Remove lookup after page has been closed
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
  margin-top: 20px;
}
table, th, td {
  border: 2px solid black;
  border-collapse: collapse;
  padding: 20px;
}
</style>