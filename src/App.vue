<template>
  <v-app>
    <v-main class="grey lighten-5">
      <v-container>


      <v-row dense>

        <v-col cols="8" style="height: 100vh; overflow: auto">
          <v-alert color="blue lighten-5 blue--text">

            <h2>Welcome to nutrition analyzer</h2>
            <p>
              Nutrition analyzer is a simple web application that will help you find out the nutrients in the collection
              of food<br>
              Begin by adding your choice of food to the list to analyze.
            </p>
          </v-alert>

          <v-row>
            <v-col cols="12" sm="3" v-for="food in sortedList" :key="food.label">
              <v-card flat rounded>
                <v-img height="200px" :src="food.photo_url"></v-img>

                <v-card-title>
                  {{ food.label }}
                </v-card-title>
                <v-card-text>

                </v-card-text>

                <v-card-actions>
                  <v-btn @click="addItem(food)" color="blue" dark depressed block large>Add to list</v-btn>
                </v-card-actions>
              </v-card>

            </v-col>

          </v-row>
        </v-col>

        <v-divider vertical></v-divider>
        <v-col cols="4">

          <v-simple-table class="mt-4" v-if="selectedList.length>0">
            <thead>
            <tr>
              <th colspan="3">

                <h2>Selected Food items:</h2>
              </th>
            </tr>
            </thead>
            <tbody>
            <tr v-for="(item,index) in selectedList" :key="item.label">
              <td>
                <strong>

                  {{ item.label }}:

                </strong>
              </td>


              <td>
                <v-text-field v-model="item.quantity" label="Quantity"></v-text-field>
              </td>
              <td>
                <v-select
                    label="Unit"
                    :items="sortedUnits"
                    v-model="item.unit"
                ></v-select>
              </td>
              <td>
                <v-btn @click="selectedList.splice(index,1)" text color="red" small>Remove</v-btn>
              </td>
            </tr>

            <tr>
              <td colspan="4">
                <v-btn @click="analyze" color="blue" dark block large depressed>Analyze</v-btn>
              </td>
            </tr>
            </tbody>
          </v-simple-table>


          <h2 v-else class="text-center pa-5">You have not added any food to the list yet</h2>

        </v-col>
      </v-row>

      <v-dialog width="30%" v-model="resultsDialog" persistent scrollable>
        <v-card flat>
          <v-card-title>
            <h4 class="font-weight-light">Nutrition analysis results</h4> <v-spacer></v-spacer><v-btn @click="resultsDialog=false" icon><v-icon>mdi-close</v-icon></v-btn>
          </v-card-title>
          <v-card-text v-if="results">

            <center v-if="progress">
              <v-progress-circular indeterminate color="blue" size="70" v-if="progress"></v-progress-circular>
            </center>
            <span v-else>

              <h1 class="font-weight-black text-center">Nutrition Facts</h1>
              <hr>

              <h3>Amount per Serving:</h3>

              <v-simple-table>
                <tbody>
                <tr>
                  <td>

              <h2> Total Calories:</h2>
                  </td>
                  <td style="text-align: right">
                     {{results.calories}}
                  </td>
                </tr>
                     <tr>
                  <td>

              <h2> Total Weight:</h2>
                  </td>
                  <td style="text-align: right">
                     {{results.totalWeight}}
                  </td>
                </tr>


                <tr>
                  <td colspan="2" class="font-weight-black" style="text-align: right">
                    % Daily Value*
                  </td>
                </tr>

                <tr v-for="k in dailyKeys" :key="k">
                  <td>
                    <strong>{{results.totalDaily[k].label}}:</strong>
                  </td>

                    <td style="text-align: right">
                 {{results.totalNutrients[k].quantity.toFixed(2)}}{{results.totalNutrients[k].unit}} ({{results.totalDaily[k].quantity.toFixed(2)}}{{results.totalDaily[k].unit}})
                  </td>
                </tr>


                <tr>
                  <td colspan="2">
                    <h2 class="font-weight-black text-center">Calories Facts</h2>
                  </td>
                </tr>


                <tr v-for="k in calKeys" :key="k">
                  <td>
                    <strong>{{results.totalNutrientsKCal[k].label}}:</strong>
                  </td>

                    <td style="text-align: right">
                 {{results.totalNutrientsKCal[k].quantity.toFixed(2)}}{{results.totalNutrientsKCal[k].unit}}
                  </td>
                </tr>


                </tbody>
              </v-simple-table>
            </span>


          </v-card-text>
        </v-card>
      </v-dialog>
      </v-container>

    </v-main>
  </v-app>
</template>

<script>

export default {
  name: 'App',

  components: {},

  data: () => ({
    resultsDialog:false,
    results:null,
    base_URL:"https://api.edamam.com/api/nutrition-data?app_id=756f1379&app_key=72a70f51d7f4f7a76c2b9f7cd417134f",
    progress: false,
    units: [
      {
        "value": "mg",
        "text": "Milligram"
      },
      {
        "value": "g",
        "text": "Gram"
      },
      {
        "value": "kg",
        "text": "Kilogram"
      },
      {
        "value": "cup",
        "text": "Cup"
      },
      {
        "value": "oz",
        "text": "ounce"
      }
      ,
      {
        "value": "tuber",
        "text": "Tuber"
      }
      ,
      {
        "value": "litre",
        "text": "Litre"
      },
      {
        "value": "bottle",
        "text": "Bottle"
      }

    ],
    selectedList: [],

    foods: [
      {
        "label": "Okra",
        "photo_url":"/img/okra.jpeg"

      },
      {
        "label": "Water melon",
        "photo_url":"/img/melon.jpeg"

      },
      {
        "label": "Yam",
        "photo_url":"/img/yam.png"

      },

      {
        "label": "Beans",
        "photo_url":"/img/beans.jpeg"

      },
      {
        "label": "Fish",
        "photo_url":"/img/fish.jpeg"

      },
      {
        "label": "Potatoes",
        "photo_url":"/img/potatos.jpeg"

      },
      {
        "label": "Plantain",
        "photo_url":"/img/plantain.jpeg"

      },
      {
        "label": "Spaghetti",
        "photo_url":"/img/spaghetti.jpeg"

      },
      {
        "label": "Mangoes",
        "photo_url":"/img/mangoes.jpeg"

      },
      {
        "label": "Bread",
        "photo_url":"/img/bread.jpeg"

      },
      {
        "label": "Egg",
        "photo_url":"/img/egg.jpeg"

      },
      {
        "label": "Banana",
        "photo_url":"/img/banana.jpeg"

      },
      {
        "label": "Orange",
        "photo_url":"/img/orange.jpeg"

      },
      {
        "label": "Apple",
        "photo_url":"/img/apple.jpeg"
      },
      {
        "label": "Pear",
        "photo_url":"/img/pear.jpeg"

      },
      {
        "label": "Carrot",
        "photo_url":"/img/carrot.jpeg"

      },

    ]
  }),
  computed: {

    sortedList(){
      // eslint-disable-next-line vue/no-side-effects-in-computed-properties
      return this.foods.sort((a,b)=>a.label.localeCompare(b.label));
    },
    dailyKeys(){

      return this.results && this.results.totalDaily ? Object.keys(this.results.totalDaily) : [];
    },
       calKeys(){

      return this.results && this.results.totalNutrientsKCal ? Object.keys(this.results.totalNutrientsKCal) : [];
    },

    sortedUnits() {
      // eslint-disable-next-line vue/no-side-effects-in-computed-properties
      return this.units.sort((a, b) => a.text.localeCompare(b.text));
    },
    itemsQuery(){
      let term = "";
      this.selectedList.forEach(item=>{
        term+=", "+item.quantity+" "+item.unit+" "+item.label;
      });

      return term;

    }
  },
  methods: {
    analyze() {
      this.resultsDialog=true;
      this.progress=true;
      console.log(this.itemsQuery);
      const url = new URL(this.base_URL);
      url.searchParams.set("ingr",this.itemsQuery);


      window.axios.get(url.href)
      .then(res=>{
        this.progress=false;
        this.results = res.data;
      })

    },
    addItem(item) {
      const selectedItem = item;
      selectedItem.quantity = 1;
      selectedItem.unit = "";
      this.selectedList.push(selectedItem);

    }
  }
};
</script>
