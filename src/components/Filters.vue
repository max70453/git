<template>
  <v-container fluid>
    <!-- выбор страны -->
      <custom-title :title="'Страна'" />
        <v-autocomplete  class="mt-3"
          v-model="selectedCountry"
          :items="countries"
          dense
          outlined
          prepend-inner-icon="mdi-magnify"
          placeholder="Поиск стран"
        ></v-autocomplete>
        <!-- выбор типа -->
          <custom-title :title="'Тип'" />
          <v-select
            v-model="selectedType"
            :items="types"
            label="Тип"
            multiple
            outlined
            dense
          ></v-select>
          <!-- выбор звезд -->
           <custom-title :title="'Количество звезд'" />
           <div class="mb-6">
          <v-checkbox v-for="(star, index) in stars" :key="index" 
                dense 
                hide-details
                v-model="selectedStar"
                :label="star"
                :value="index + 1"
            ></v-checkbox>
           </div>
           <!-- ввод количества отзывов -->
        <custom-title :title="'Количество отзывов (от)'" />
        <v-text-field
          outlined
          dense
          placeholder="Например, от 10"
          v-model.number="selectedReviews"
        ></v-text-field>
        <!-- слайдер цен -->
        <custom-title :title="'Цена'" />
         <v-range-slider
            v-model="range"
            :max="max"
            :min="min"
            hide-details
            class="align-center"
            color="success"
          >
            <template v-slot:prepend>
              <v-text-field
                :value="range[0]"
                class="mt-0 pt-0"
                type="number"
                style="width: 60px"
                @change="$set(range, 0, $event)"
              ></v-text-field>
            </template>
            <template v-slot:append>
              <v-text-field
                :value="range[1]"
                class="mt-0 pt-0"
                type="number"
                style="width: 60px"
                @change="$set(range, 1, $event)"
              ></v-text-field>
            </template>
          </v-range-slider>
          <v-btn  color="success" block class="mt-8" @click="filterHotels()">Применить фильтр</v-btn>
        <v-btn  color="error" block class="mt-3" @click="clearFilter()"> 
          <v-icon dark>
            mdi-close
          </v-icon>
          очистить фильтр</v-btn>
  </v-container>
</template>

<script>
  export default {
    props: ['hotels'],
    data: () => ({
        countries: [ 'Греция', 'Албания', 'Азербайджан' ],
        types: [ 'Отель', 'Апартаменты' ],
        stars: [ '1 звезда','2 звезды','3 звезды','4 звезды','5 звезд'],
        selectedCountry: null,
        selectedType: [],
        resultFilter: {},
        selectedReviews: null,
        selectedStar: [],
        min: 0,
        max: 10000,
        range: [0, 10000],
    }),
    components:{
      CustomTitle: () => import('./CustomTitle.vue'),
    },
    created(){
      this.countries.sort();
    },
    methods:{
      filterHotels(){
        let hotels = [];
        if(this.selectedCountry){
         hotels = this.hotels.filter(el => { return el.country == this.selectedCountry})
                .filter(el => {if(this.selectedType.length > 0)
                                return this.selectedType.find(t => el.type == t)
                                else return el;})
                .filter(el => {if(this.selectedStar.length > 0)
                                return this.selectedStar.find(s => el.stars == s)
                                else return el;})
                .filter(el => {if(this.selectedReviews)
                              return Math.floor(this.selectedReviews) == el.reviews_amount
                              else return el;})
                .filter(el => {if(this.range[0] == 0 && this.range[1] == 0) return;
                                else  return (
                                el.min_price >= this.range[0] &&
                                el.min_price <= this.range[1]
                              );})
        }
        this.$emit('resultData', hotels)
      },
      clearFilter(){
        this.selectedCountry = null;
        this.selectedType =  [];
        this.selectedReviews =  null;
        this.selectedStar = [];
        this.range = [0, 10000];
        this.$emit('resultData', []);
      }
    },
  }
</script>
