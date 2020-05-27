<template>
  <div class="container">
    <div class="row text-center text-secondary pt-5">
      <div class="col pt-5">
        <h1> <i class="fas fa-chevron-left fa-xs pr-5"></i> {{ today.toDateString() }} <i class="fas fa-chevron-right fa-xs pl-5 "></i></h1>
      </div>
    </div>
    <div class="row p-5 mt-3">
      <div class="col text-secondary">
        <h5 class="border text-center text-uppercase font-weight-bold pt-2 pb-2 letter-spacing">
          log
        </h5>
        <h5 class="text-center pt-4"> how much water did you drink? </h5>
          <div class="row pt-3 ml-0 btn-block">
          <div class=" mt-0 pb-3 addDrink" v-for="(d, index) in drinks" :key="index">
            <button @click="addDrink(d); computeDrinks(d); totalCalc(); imgSrcCompute(); clickCountCheck();" class="btn btn-outline-success btn-block">
              <img :src="d.imgSrc" height="100px" width="100px">
              {{ d.title }} ({{ d.size }} cups)</button>
          </div>
            <router-link :to="{ name: 'AddDrink' }">
              <button class="btn btn-outline-success mt-3 btn-block"> custom </button>
            </router-link>
        </div> <br>

       <br>

        <!-- LIST DRINK LOG AND SHOW DELETE BUTTON -->
<!--        <div class="row pt-3" v-for="drinkLog in drinkLog" :key="drinkLog.id">-->
<!--          <div class="col">-->
<!--            <p>-->
<!--              <i class="fas fa-trash-alt" @click="deleteDrink(drinkLog.id)"></i>-->
<!--              {{ drinkLog.title }} {{ drinkLog.size}}oz-->
<!--            </p>-->
<!--          </div>-->
<!--        </div>-->

      </div>

      <div class="col text-secondary ml-5  pl-5">
        <h5 class="border text-center text-uppercase font-weight-bold pt-2 pb-2 letter-spacing">
          goal
        </h5>
        <h5 class="text-center pt-4" v-for="(f, index) in formResult" :key="index"> {{ f.hiddenResults }} cups </h5>

        <img v-bind:src="imgSrc" />
<!--        <img src="@/assets/JS2_Drop_Water_Drop_0.svg" width="300" height="300" class="img-fluid pl-5 ml-5">-->
        <br>

        <div v-if="firstDrinkAward">
          <div>
            <h5 class="border text-center text-uppercase font-weight-bold pt-2 pb-2 letter-spacing">
              awards
            </h5>
            <img src="@/assets/JS2_Drop_Water_Drop_FirstDrinkBadge.svg" width="150px" height="150px">

            <div v-if="firstAward">
              <img src="@/assets/JS2_Drop_Water_Drop_FirstBadge.svg" width="150px" height="150px">
            </div>
          </div>
        </div>


<!--        <div v-if="firstAward">-->
<!--            <img src="@/assets/JS2_Drop_Water_Drop_FirstBadge.svg" width="150px" height="150px">-->
<!--        </div>-->

        <!-- FOR TESTING -- OUTPUT ARRAY AND TOTALCALC  -->
        <!-- total: {{ total }}            total calculation: {{ totalCalculation }} -->

<!--          <p v-for="total in total" :key="total.id">-->
<!--          total: {{ total }} <br>-->
<!--            total calculation: {{ totalCalculation }}-->
<!--        </p>-->
      </div>
    </div>
  </div>
</template>

<script>
import db from '@/firebase/init'

export default {
    name: 'Index',
    data() {
        return {
            drinks: [],
            drinkLog: [],
            today: new Date(),
            total: [],
            formResult: [],
            totalCalculation: 0,
            imgSrc: './assets/JS2_Drop_Water_Drop_0.svg',
            hiddenResults: '',
            firstAward: false,
            firstDrinkAward: false,
            iconImgSrc: '',
            clickCount: 0,
        }
    },
    methods: {
        deleteDrink(id) {
            // delete doc from firestore
            db.collection('drinkLog').doc(this.today.toDateString()).collection('drinks').doc(id).delete()
                .then(() => {
                    this.drinkLog = this.drinkLog.filter(drink => {
                        return drink.id != id
                    })
                })
        },
        addDrink(d) {
            db.collection('drinkLog').doc(this.today.toDateString()).collection('drinks').add({
                title: d.title,
                size: d.size,
                slug: d.slug
            }).then(() => {
                console.log('Write succeeded!')
                // window.location.reload()
            }).catch(err => {
                console.log(err)
            })
        },
        computeDrinks(d) {

            let total = d.size;
            this.total.push(total)

        },
        totalCalc() {

            this.totalCalculation = 0;
                for (let i = 0; i < this.total.length; i++) {
                    this.totalCalculation += this.total[i]
                }
        },
      clickCountCheck() {
          this.clickCount = 1;
            if(this.clickCount === 1){
              alert('woo hoo, you added your first drink!')
              this.firstDrinkAward = true;
              removeEventListener("clickCountCheck")
            }
      },
        imgSrcCompute() { // this will need to calculate based on their total[]

            // TODO: Goal of 5 cups (100lbs), and 11-16 cups

            // goal of 4 cups -- 80lbs
            if (this.hiddenResults === 4) {
                // 0%
                if (this.totalCalculation === 0) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_0.svg";
                }
                // 25%
                 if (this.totalCalculation >= 1) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_2.svg";
                }
                // 50%
                if (this.totalCalculation >= 2) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_5.svg";
                }
                // 75%
                if (this.totalCalculation >= 3) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_7.svg";
                }
                // 100%
                if (this.totalCalculation >= 4) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_10.svg";
                    alert('good job, you reached your goal!')
                }
            }
            // goal of 6 cups -- 120lbs
            else if (this.hiddenResults === 6) {
                // if(this.totalCalculation){
                //
                // }
                // 0%
                if (this.totalCalculation === 0) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_0.svg";
                }
                // 17% (showing 20%)
                if (this.totalCalculation >= 1) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_2.svg";
                }
                // 34% (showing 30%)
                if (this.totalCalculation >= 2) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_3.svg";
                }
                // 51% (showing 50%)
                if (this.totalCalculation >= 3) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_5.svg";
                }
                // 68% (showing 70%)
                if (this.totalCalculation >= 4) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_7.svg";
                }
                // 85% (showing 90%)
                if (this.totalCalculation >= 5) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_9.svg";
                }
                // 102% (showing 100%)
                if (this.totalCalculation >= 6) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_10.svg";
                    alert('good job, you reached your goal!')
                    this.firstAward = true;
                }
            }
            // goal of 7 cups -- 140lbs
            else if (this.hiddenResults === 7) {
                // 0%
                if (this.totalCalculation === 0) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_0.svg";
                }
                // 14% (showing 10%)
                if (this.totalCalculation >= 1) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_1.svg";
                }
                // 28% (showing 30%)
                if (this.totalCalculation >= 2) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_3.svg";
                }
                // 42% (showing 40%)
                if (this.totalCalculation >= 3) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_4.svg";
                }
                // 56% (showing 50%)
                if (this.totalCalculation >= 4) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_5.svg";
                }
                // 70% (showing 70%)
                if (this.totalCalculation >= 5) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_7.svg";
                }
                // 84% (showing 80%)
                if (this.totalCalculation >= 6) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_8.svg";
                }
                // 98% (showing 100%)
                if (this.totalCalculation >= 6) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_10.svg";
                    alert('good job, you reached your goal!')
                }
            }
            // goal of 8 cups -- 160lbs
            else if (this.hiddenResults === 8) {
                // 0%
                if (this.totalCalculation === 0) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_0.svg";
                }
                // 13% (showing 10%)
                if (this.totalCalculation >= 1) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_1.svg";
                }
                // 26% (showing 30%)
                if (this.totalCalculation >= 2) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_3.svg";
                }
                // 39% (showing 40%)
                if (this.totalCalculation >= 3) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_4.svg";
                }
                // 52% (showing 50%)
                if (this.totalCalculation >= 4) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_5.svg";
                }
                // 65% (showing 70%)
                if (this.totalCalculation >= 5) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_7.svg";
                }
                // 78% (showing 80%)
                if (this.totalCalculation >= 6) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_8.svg";
                }
                // 91% (showing 90%)
                if (this.totalCalculation >= 7) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_10.svg";
                }
                // 104% (showing 90%)
                if (this.totalCalculation >= 8) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_10.svg";
                    alert('good job, you reached your goal!')
                }
            }
            // goal of 9 cups -- 180lbs
            else if (this.hiddenResults === 9) {
                // 0%
                if (this.totalCalculation === 0) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_0.svg";
                }
                // 11% (showing 10%)
                if (this.totalCalculation >= 1) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_1.svg";
                }
                // 22% (showing 20%)
                if (this.totalCalculation >= 2) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_2.svg";
                }
                // 33% (showing 30%)
                if (this.totalCalculation >= 3) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_3.svg";
                }
                // 44% (showing 40%)
                if (this.totalCalculation >= 4) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_4.svg";
                }
                // 55% (showing 50%)
                if (this.totalCalculation >= 5) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_5.svg";
                }
                // 66% (showing 60%)
                if (this.totalCalculation >= 6) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_6.svg";
                }
                // 77% (showing 70%)
                if (this.totalCalculation >= 7) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_7.svg";
                }
                // 77% (showing 80%)
                if (this.totalCalculation >= 8) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_8.svg";
                }
                // 77% (showing 90%)
                if (this.totalCalculation >= 9) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_10.svg";
                    alert('good job, you reached your goal!')

                }
            }
            // goal of 10 cups -- 200lbs
            else if (this.hiddenResults === 10) {
                // 0%
                if (this.totalCalculation === 0) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_0.svg";
                }
                // 10%
                if (this.totalCalculation >= 1) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_1.svg";
                }
                // 20%
                if (this.totalCalculation >= 2) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_2.svg";
                }
                // 30%
                if (this.totalCalculation >= 3) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_3.svg";
                }
                // 40%
                if (this.totalCalculation >= 4) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_4.svg";
                }
                // 50%
                if (this.totalCalculation >= 5) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_5.svg";
                }
                // 60%
                if (this.totalCalculation >= 6) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_6.svg";
                }
                // 70%
                if (this.totalCalculation >= 7) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_7.svg";
                }
                // 80%
                if (this.totalCalculation >= 8) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_8.svg";
                }
                // 90%
                if (this.totalCalculation >= 9) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_9.svg";}
                // 100%
                if (this.totalCalculation >= 10) {
                    this.imgSrc = "./assets/JS2_Drop_Water_Drop_10.svg";
                    alert('good job, you reached your goal!')
                }
            }
        }

    },
    computed: {
        // totalCalculation(total){
        //
        //     let sum = 0;
        //     for (let i = 0; i < total.length; i++) {
        //         sum += total[i]
        //     }
        //     // total += total;
        //     // return total;
        //
        // },

        },
        created() {
            // fetch data from firestore
            db.collection('drinks').get()
                .then(snapshot => {
                    snapshot.forEach(doc => {
                        let drink = doc.data()
                        drink.id = doc.id
                        this.drinks.push(drink)
                    })
                }),
                db.collection('drinkLog').doc(this.today.toDateString()).collection('drinks').get()
                    .then(snapshot => {
                        snapshot.forEach(doc => {
                            let drinkLog = doc.data()
                            drinkLog.id = doc.id
                            this.drinkLog.push(drinkLog)
                        })
                    }),
                db.collection('form').get()
                    .then(snapshot => {
                        snapshot.forEach(doc => {
                            let form = doc.data()
                            form.id = doc.id
                            this.formResult.push(form)
                            this.hiddenResults = form.hiddenResults
                        })
                    })

        }

}
</script>


<style>
  .letter-spacing {
    letter-spacing: 5px;
  }

  .addDrink {
    cursor: pointer;
  }

  .btn-outline-success {
    border-color: #bdd276 !important;
    color: #bdd276 !important;
  }
  .text-success {
    color: #bdd276 !important;
  }
</style>
