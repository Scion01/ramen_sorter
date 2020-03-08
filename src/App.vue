<template>
  <v-app>
    <v-toolbar
        dark
        prominent
        src="https://imgix.bustle.com/mic/zokiuev9rmgdvp1c9rcfmq1felhbz8tzeczi0weyv9icnjtdzh3hohe4rplnzpso.jpg?w=1020&h=576&fit=crop&crop=faces&auto=format&q=70"
      >
        <v-toolbar-title>Ramen Sorter</v-toolbar-title>
        <v-spacer></v-spacer>
  
        <v-btn icon>
          <v-icon>mdi-help</v-icon>
        </v-btn>
      </v-toolbar>
      <div style="height: 100%;">
      <v-card>
      <v-card-title>
        The restaurants
        <v-spacer></v-spacer>
        <v-autocomplete
          v-model="search"
          append-icon="mdi-magnify"
          label="Sort by country"
          single-line
          hide-details
          :items="countries"
          dense
          chips
          small-chips
          multiple
          solo
          deletable-chips
        ></v-autocomplete>
      </v-card-title>
        <v-data-table
          :headers="headers"
          :items="allrestaurants"
          :loading="loadingData"
          class="elevation-1"
          loading-text="Loading... Please wait"
          no-data-text="No restaurants Found!"
        >
        <template v-slot:item="{item}">
          <tr @click="itemClicked(item)" class="pointed" >
            <td class="text-xs-left">{{ item.Brand | capitalize  }}</td>
            <td class="text-xs-left">{{ item.Variety | capitalize }}</td>
            <td class="text-xs-left">{{ item.Style | capitalize }}</td>
            <td class="text-xs-left">{{ item.Country | capitalize }}</td>
            <v-chip :color="setStarsColor(item.Stars)" >{{ item.Stars | checkExists }}</v-chip>
            <td class="text-xs-left">{{ item['Top Ten']  | checkExists}}</td>
          </tr>
        </template></v-data-table>
        <!-- :search="search" -->
    </v-card>
    <v-dialog v-model="descriptionDialog" max-width="600">
       <v-card
      class="mx-auto"
      max-width="600"
     
    >
      <v-img
        class="white--text align-end"
        height="200px"
        :src="dialogImgLink"
      >
     
        <v-card-title >{{cardVal["Brand"]}}</v-card-title>
        
      </v-img>
  
      <v-card-subtitle class="pb-1">Variety: {{cardVal["Variety"]}}</v-card-subtitle>
  
      <v-card-text class="text--primary">
        <v-layout row wrap>
        <div class="ml-2">Country: <img :src="cardVal['flag']" /> {{cardVal["Country"]}}</div>
        <div style="margin-left:auto; margin-right:0;"><v-icon small>mdi-phone</v-icon> {{cardVal["phone"]}}</div>
        </v-layout>
         <v-layout row wrap>
        <div v-if="rating" class="ml-2">Rating: <v-rating
        v-model="rating"
        background-color="red lighten-3"
        color="red"></v-rating></div>
        <div style="text-align:right; margin-left:auto; margin-right:0;">
        <v-btn @click="likeThisElem(cardVal)" class="ma-2" tile large color="red" icon>
        <v-icon>mdi-heart</v-icon>
      </v-btn>
        </div>
         </v-layout>
      </v-card-text>
  
      <!-- <v-card-actions>
        <v-btn
          color="orange"
          text
        >
          Share
        </v-btn>
  
        <v-btn
          color="orange"
          text
        >
          Call
        </v-btn>
      </v-card-actions> -->
    </v-card>
    </v-dialog>
    <v-snackbar
        v-model="snackbar"
        top
        color="black"
        :timeout="timeout"
      >
        {{ snackText }}
        <v-btn
          color="white"
          text
          @click="snackbar = false"
        >
          Close
        </v-btn>
      </v-snackbar>
      </div>
  </v-app>
</template>
<script>
// import { countries } from "country-flags-svg";

export default {
  name: 'App',
  components: {
  },
  data: () => ({
    search: [],
    dialogImageLinks: ['https://thechutneylife.com/wp-content/uploads/2019/12/Peanut-Ramen-Noodle-Soup-The-Chutney-Life-8.jpg',
    'https://www.recipetineats.com/wp-content/uploads/2019/01/Chicken-Ramen-Noodles_2a.jpg',
    'https://www.ft.com/__origami/service/image/v2/images/raw/https%3A%2F%2Fs3-ap-northeast-1.amazonaws.com%2Fpsh-ex-ftnikkei-3937bb4%2Fimages%2F3%2F8%2F1%2F9%2F22199183-3-eng-GB%2FCropped-1566246172%E5%B9%B8%E6%A5%BD%E8%8B%91%E3%81%8C%E8%B2%A9%E5%A3%B2%E3%81%99%E3%82%8B%E3%83%9F%E3%83%89%E3%83%AA%E3%83%A0%E3%82%B7%E3%82%92%E7%B7%B4%E3%82%8A%E8%BE%BC%E3%82%93%E3%81%A0%E3%81%A4%E3%81%91%E9%BA%BA20190820050542424_Data.jpg?source=nar-cms'],
    loadingData: true,
    dialogImgLink: '',
    link: 'http://starlord.hackerearth.com/TopRamen',
    countries: [],
      headers: [
        {
          text: 'Brand Name',
          align: 'start',
          sortable: false,
          value: 'Brand',
        },
        { text: 'Variety', value: 'Variety' },
        { text: 'Style', value: 'Style' },
        { text: 'Country', value: 'Country' },
        { text: 'Stars', value: 'Stars' },
        { text: 'Top Ten', value: 'Top Ten' },
      ],
      restaurants: [],
      allrestaurants: [],
      descriptionDialog: false,
      cardVal: {},
      rating: false,
      snackText: '',
      snackbar: false,
      timeout: 5000,  
  }),
  methods:{
    itemClicked(val){
      this.cardVal = val;
      this.rating = val.Stars;
      this.dialogImgLink = this.dialogImageLinks[Math.floor((Math.random() * 100)%3)];
      this.descriptionDialog = true;
    },
    setStarsColor(val){
        if (val > 4) return 'green'
      else if (val > 3) return 'orange'
      else return 'red'
    },
    generateRandomPhone(){
      return Math.floor(100000000 + Math.random() * 900000000);
    },
    likeThisElem(ele){
      this.snackText = "Restraunt Liked!!!";
      this.snackbar = true;
      ele['liked'] = true;
    }
  },
  created(){
    const http = new XMLHttpRequest();
    http.open("GET", this.link, true);
    let context = this;
    http.setRequestHeader("content-type", "application/json");
    http.setRequestHeader("accept", "application/json");
    http.setRequestHeader("Access-Control-Allow-Origin", "*");
    http.onreadystatechange = () => {
      if (http.readyState === 4) {
        if(http.status === 200) {
          if(http.responseText) {
            //  console.log((JSON.parse(http.responseText)));
            //  console.log(countries)
            context.restaurants = JSON.parse(http.responseText);
            context.countries = context.restaurants.map((elem)=>{
              return elem["Country"];
            })
            context.allrestaurants = context.restaurants;
            context.allrestaurants.forEach((e)=>{
              e['phone'] = context.generateRandomPhone();
              // console.log(e.Country)
              // for(var c in countries){
              //   console.log(c['name'])
              //   console.log(e.Country)
              //   if(c['name'] == e.Country){
              //     e['flag'] = c['flag'];
              //     console.log( e['flag'])
              //   }
              // }
            });
            // console.log(context.countries);
            context.loadingData =false;
            context.snackText = `${context.allrestaurants.length} results found!`
            context.snackbar = true;
          } else { 
            console.log("No data returned!!!")
            context.loadingData =false;
          }
        } else {
          context.loadingData =false;
          // vm.errorStatus = true;
          // vm.errMsg = "Not Authorized!"
          // return reject(http);
        }
      }
    };
    http.send();
  },
  watch:{
    search(val){
      if(val.length == 0){
        this.allrestaurants = this.restaurants;
      }else{
        this.allrestaurants = this.restaurants.filter((elem)=>{
          return val.includes(elem['Country'])
        })
      }
    }
  },
  filters: {
    capitalize(string) {
      if (!string) return ''
      return `${string.charAt(0).toUpperCase()}${string.substring(1)}`
    },
    checkExists(val){
      if(val == "NaN")
        return "Not Found!";
      return val;
    }
  }
};
</script>
<style scoped>
.pointed {
  cursor: pointer;
}
</style>
