<template>
  <v-content>
    <v-container grid-list-xl text-xs-center>
      <v-layout row wrap>
        <v-flex xs8 offset-xs2>
          <v-alert
            v-if="error"
            :value="true"
            type="error"
          >
            {{ error }}
          </v-alert>
          <div v-if="results.name" class='pb-4'>
            <h1>Report Result</h1>
            <h3>Alderman Name: {{ results.name }}</h3>
            <h3>Ward: {{ results.ward }}</h3>
            <h3>Search month: {{ results.month }}</h3>
            <h3>Graffiti Reports: {{ results.reportsCount }}</h3>
          </div>
          <v-card dark class='pa-3'>
            <v-card-text><h2>Create Graffiti Report</h2></v-card-text>
            <v-form ref="form">
              <v-text-field
                v-model="aldermanName"
                :rules="nameRules"
                label="Alderman last name"
                required
              ></v-text-field>
              <v-select
                :items="months"
                v-model="selectedMonth"
                :rules="monthRules"
                label="Search month"
              ></v-select>
              <v-select
                :items="years"
                v-model="selectedYear"
                :rules="yearRules"
                label="Search year"
              ></v-select>
              <v-btn
                @click="submit"
              >
                submit
              </v-btn>
              <v-btn @click="clear">clear</v-btn>
            </v-form>
          </v-card>
        </v-flex>
      </v-layout>
    </v-container>
  </v-content>
</template>

<script>
  import axios from 'axios';

  export default {
    data: () => ({
      aldermanName: '',
      months: Array.from({length:12},(v,k)=>k+1),
      years: [2015,2016,2017,2018],
      selectedMonth: '',
      selectedYear: '',
      nameRules: [
        v => !!v || 'Name is required',
      ],
      monthRules: [
        v => !!v || 'Month is required',
      ],
      yearRules: [
        v => !!v || 'Year is required',
      ],
      results: {
        name: '',
        ward: '',
        month: '',
        reportsCount: ''
      },
      error: ''
    }),
    methods: {
      submit () {
        if (this.$refs.form.validate()) {
          axios.get(`http://localhost:3000/grafitti_reports/create_report`, {
            params: {
              name: this.aldermanName,
              search_date: this.formattedDate
            }
          })
          .then(response => {
            this.results.name = response.data.alderman_name
            this.results.ward = response.data.ward_number
            this.results.month = response.data.report_month
            this.results.reportsCount = response.data.number_of_reports
            this.$refs.form.reset()
          })
          .catch(e => {
            this.error = 'No results found, Try again'
            this.$refs.form.reset()
          })
        }
      },
      clear () {
        this.$refs.form.reset()
      }
    },
    computed: {
      formattedDate () {
        if ( this.selectedMonth < 10 ) {
          var fullMonth = "0" + this.selectedMonth
        }
        return fullMonth + "/" + this.selectedYear
      }
    }
  }
</script>