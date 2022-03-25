<template>
  <div class="home">
    <v-container>
      <v-row>
        <v-col cols="12">
          <h2 class="text-center">
          </h2>
        </v-col>
        <v-col cols="12">
          <v-row>
            <v-col cols="3">
              <v-select :items="cards" v-model="selectedCard" label="Card" />
            </v-col>
            <v-col cols="3">
              <v-row>
                <v-col cols="6">
                  <v-select :items="profits" v-model="selectedPrice" label="Long term profit rate"/>
                </v-col>
                <v-col cols="6">
                  <v-text-field v-model="currentCard.profit[selectedPrice]" label="Profit rate value" prepend-icon="mdi-currency-usd"/>
                </v-col>
              </v-row>
            </v-col>
            <v-col cols="3">
              <v-row>
                <v-col cols="6">
                  <v-text-field v-model="currentCard.cost" prepend-icon="mdi-currency-usd" label="Card cost"/>
                </v-col>
                <v-col cols="6">
                  <v-text-field v-model="electricCost" prepend-icon="mdi-currency-usd" label="Cost/kwh"/>
                </v-col>
              </v-row>
            </v-col>
            <v-col cols="3">
              <v-row>
                <v-col cols="6">
                  <v-text-field v-model="currentCard.startingCards" label="Starting GPU count"/>
                </v-col>
                <v-col cols="6">
                  <v-switch v-model="includeBuyingEnclosures" label="Fund enclosures" />
                </v-col>
              </v-row>
            </v-col>
          </v-row>
        </v-col>
        <v-col cols="12">
          <v-tabs>
            <v-tab>
              Generic info
            </v-tab>
            <v-tab>
              Return over 1 year
            </v-tab>
            <v-tab>
              Enclosure Settings
            </v-tab>
            <v-tab-item>
              <v-row class="pa-3">
                <v-col cols="6">
                  <v-card>
                    <v-card-title>
                      {{selectedCard}} return on investment per card
                    </v-card-title>
                    <v-card-text>
                      <v-row>
                        <v-col cols="4">
                          Low
                        </v-col>
                        <v-col cols="4">
                          Med
                        </v-col>
                        <v-col cols="4">
                          High
                        </v-col>
                        <v-col cols="4">
                          {{Math.ceil(currentCard.cost/currentCard.profit.low)}} days
                        </v-col>
                        <v-col cols="4">
                          {{Math.ceil(currentCard.cost/currentCard.profit.med)}} days
                        </v-col>
                        <v-col cols="4">
                          {{Math.ceil(currentCard.cost/currentCard.profit.high)}} days
                        </v-col>
                        <v-col cols="4">
                          {{round((currentCard.cost/currentCard.profit.low)/7)}} weeks
                        </v-col>
                        <v-col cols="4">
                          {{round((currentCard.cost/currentCard.profit.med)/7)}} weeks
                        </v-col>
                        <v-col cols="4">
                          {{round((currentCard.cost/currentCard.profit.high)/7)}} weeks
                        </v-col>
                        <v-col cols="4">
                          {{round((currentCard.cost/currentCard.profit.low)/30)}} months
                        </v-col>
                        <v-col cols="4">
                          {{round((currentCard.cost/currentCard.profit.med)/30)}} months
                        </v-col>
                        <v-col cols="4">
                          {{round((currentCard.cost/currentCard.profit.high)/30)}} months
                        </v-col>
                      </v-row>
                    </v-card-text>
                  </v-card>
                </v-col>
                <v-col cols="6">
                  <v-card>
                    <v-card-title>
                      General stats
                    </v-card-title>
                    <v-card-text>
                      Cost of initial cards: ${{round(currentCard.cost * startingGPUCount)}}<br />
                      Cost of taxes: ${{round(currentCard.cost * startingGPUCount * 1.06 - (currentCard.cost * startingGPUCount))}}<br />
                      Total cost: <b>${{round(currentCard.cost * startingGPUCount * 1.06)}}</b>
                    </v-card-text>
                  </v-card>
                </v-col>
              </v-row>
            </v-tab-item>
            <v-tab-item>
              <v-row class="pa-3">
                <v-col cols="12">
                  <v-card>
                    <v-card-text>
                      <v-simple-table width="100%">
                        <template v-slot:default>
                          <thead :key="key">
                            <tr>
                              <th class="text-left">Month</th>
                              <th class="text-left">Total GPUs</th>
                              <th class="text-left">Income/mo</th>
                              <th class="text-left">Income/yr</th>
                              <th class="text-left">Taxes this month</th>
                              <th class="text-left">Taxes total owed</th>
                              <th class="text-left">Wattage Total</th>
                              <th class="text-left">Amp Total</th>
                              <th class="text-left">Kilowatt hours</th>
                              <th class="text-left">Electricity cost</th>
                              <th class="text-left">GPUs to buy</th>
                              <th class="text-left">Enclosures to buy</th>
                              <th class="text-left">Total Rigs</th>
                            </tr>
                          </thead>
                          <tbody>
                            <tr v-for="i in 12" :key="i">
                              <td>{{i}}</td>
                              <td>{{profitCalc[i].totalCards}}</td>
                              <td>${{round(profitCalc[i].incomePerMonth)}}</td>
                              <td>${{round(profitCalc[i].incomePerYear)}}</td>
                              <td>${{round(profitCalc[i].taxes)}}</td>
                              <td>${{round(profitCalc[i].taxesTotal)}}</td>
                              <td>{{round(profitCalc[i].wattageTotal)}}</td>
                              <td>{{round(profitCalc[i].ampTotal)}}</td>
                              <td>{{round(profitCalc[i].kwhTotalMonth)}}</td>
                              <td>${{round(profitCalc[i].kwhTotalMonth * electricCost)}}</td>
                              <td>{{round(profitCalc[i].cardsToBuy)}}</td>
                              <td>{{round(profitCalc[i].rigsToBuy)}}</td>
                              <td>{{round(profitCalc[i].rigsTotal)}}</td>
                            </tr>
                          </tbody>
                        </template>
                      </v-simple-table>
                    </v-card-text>
                  </v-card>
                </v-col>
              </v-row>
            </v-tab-item>
            <v-tab-item>
              <v-row class="pa-3">
                <v-col cols="12">
                  <v-card>
                    <v-card-title>
                      Costs per enclosure
                    </v-card-title>
                    <v-card-text>
                      <v-row>
                        <v-col cols="3">
                          Item Type
                        </v-col>
                        <v-col cols="3">
                          Quantity
                        </v-col>
                        <v-col cols="3">
                          Price
                        </v-col>
                        <v-col cols="3">
                          Total
                        </v-col>
                        <v-col cols="3">
                          Motherboard
                        </v-col>
                        <v-col cols="3">
                          <v-text-field label="Required" v-model="components.motherboard.required" />
                        </v-col>
                        <v-col cols="3">
                          <v-text-field label="Price" v-model="components.motherboard.price" prepend-icon="mdi-currency-usd" />
                        </v-col>
                        <v-col cols="3">
                          ${{round(components.motherboard.required * components.motherboard.price)}}
                        </v-col>
                        <v-col cols="3">
                          RAM
                        </v-col>
                        <v-col cols="3">
                          <v-text-field label="Required" v-model="components.ram.required" />
                        </v-col>
                        <v-col cols="3">
                          <v-text-field label="Price" v-model="components.ram.price" prepend-icon="mdi-currency-usd" />
                        </v-col>
                        <v-col cols="3">
                          ${{round(components.ram.required * components.ram.price)}}
                        </v-col>
                        <v-col cols="3">
                          CPU
                        </v-col>
                        <v-col cols="3">
                          <v-text-field label="Required" v-model="components.cpu.required" />
                        </v-col>
                        <v-col cols="3">
                          <v-text-field label="Price" v-model="components.cpu.price" prepend-icon="mdi-currency-usd" />
                        </v-col>
                        <v-col cols="3">
                          ${{round(components.cpu.required * components.cpu.price)}}
                        </v-col>
                        <v-col cols="3">
                          PSU
                        </v-col>
                        <v-col cols="3">
                          <v-text-field label="Required" v-model="components.psu.required" />
                        </v-col>
                        <v-col cols="3">
                          <v-text-field label="Price" v-model="components.psu.price" prepend-icon="mdi-currency-usd" />
                        </v-col>
                        <v-col cols="3">
                          ${{round(components.psu.required * components.psu.price)}}
                        </v-col>
                        <v-col cols="3">
                          GPU Risers
                        </v-col>
                        <v-col cols="3">
                          <v-text-field label="Required" v-model="components.risers.required" />
                        </v-col>
                        <v-col cols="3">
                          <v-text-field label="Price" v-model="components.risers.price" prepend-icon="mdi-currency-usd" />
                        </v-col>
                        <v-col cols="3">
                          ${{round(components.risers.required * components.risers.price)}}
                        </v-col>
                        <v-col cols="3">
                          SSD
                        </v-col>
                        <v-col cols="3">
                          <v-text-field label="Required" v-model="components.ssd.required" />
                        </v-col>
                        <v-col cols="3">
                          <v-text-field label="Price" v-model="components.ssd.price" prepend-icon="mdi-currency-usd" />
                        </v-col>
                        <v-col cols="3">
                          ${{round(components.ssd.required * components.ssd.price)}}
                        </v-col>
                        <v-col cols="3">
                          Frame
                        </v-col>
                        <v-col cols="3">
                          <v-text-field label="Required" v-model="components.frame.required" />
                        </v-col>
                        <v-col cols="3">
                          <v-text-field label="Price" v-model="components.frame.price" prepend-icon="mdi-currency-usd" />
                        </v-col>
                        <v-col cols="3">
                          ${{round(components.frame.required * components.frame.price)}}
                        </v-col>
                      </v-row>
                      <v-row>
                        <v-col cols="12">
                          <h2>
                            Total cost: ${{enclosureCost}}
                          </h2>
                        </v-col>
                      </v-row>
                    </v-card-text>
                  </v-card>
                </v-col>
              </v-row>
            </v-tab-item>
          </v-tabs>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>
<script>
// @ is an alias to /src

export default {
  name: 'Home',
  components: {
  },
  computed: {
    key () {
      const nonsense = this.currentCard.cost + this.enclosureCost
      console.log(nonsense)
      return 12
    },
    currentCard () {
      return this.rates[this.selectedCard]
    },
    enclosureCost () {
      return this.round(
        (this.components.motherboard.required * this.components.motherboard.price) +
        (this.components.ram.required * this.components.ram.price) +
        (this.components.cpu.required * this.components.cpu.price) +
        (this.components.psu.required * this.components.psu.price) +
        (this.components.risers.required * this.components.risers.price) +
        (this.components.ssd.required * this.components.ssd.price) +
        (this.components.frame.required * this.components.frame.price)
      )
    },
    profitCalc () {
      if (this.startingGPUCount <= 0) {
        return {}
      }
      let totalProfit = 0
      let profit = 0
      let cardCount = parseInt(this.startingGPUCount)
      let taxesTotal = 0
      let enclosures = Math.ceil(this.startingGPUCount / this.components.motherboard.gpusSeated)
      const data = {}

      for (let i = 0; i < 12; i++) {
        const profitThisCycle = cardCount * this.currentCard.profit[this.selectedPrice] * 30
        const taxes = (profitThisCycle * 1.24) - profitThisCycle
        taxesTotal = taxesTotal + taxes
        let cardsToBuy = 0
        totalProfit = totalProfit + profitThisCycle
        profit = profitThisCycle + profit
        let enclosuresBought = 0

        // Spend on GPUs
        while (profit >= parseFloat(this.currentCard.cost)) {
          cardsToBuy = cardsToBuy + 1
          profit = profit - parseFloat(this.currentCard.cost)
        }

        cardCount = cardCount + cardsToBuy
        // Calculate enclosures needed
        const enclosuresNeeded = Math.ceil(cardCount / this.components.motherboard.gpusSeated)

        if (cardCount > (enclosures * this.components.motherboard.gpusSeated)) {
          if (this.includeBuyingEnclosures) {
            // Not quite enough to buy an enclosure, so just refund all cards and save up.
            if ((profit + (parseFloat(this.currentCard.cost) * cardsToBuy)) < this.enclosureCost) {
              cardCount = cardCount - cardsToBuy
              cardsToBuy = 0
              profit = profit + (parseFloat(this.currentCard.cost) * cardsToBuy)
              if (this.enclosureCost <= profit) {
                profit = profit - this.enclosureCost
              }
            } else {
              while (enclosures < enclosuresNeeded && ((parseFloat(this.currentCard.cost) * cardsToBuy) >= this.enclosureCost)) {
                while (profit < this.enclosureCost) {
                  profit = profit + parseFloat(this.currentCard.cost)
                  cardsToBuy = cardsToBuy - 1
                  cardCount = cardCount - 1
                }

                enclosures = enclosures + 1
                enclosuresBought = enclosuresBought + 1
              }
            }
          } else {
            const enclosureDiff = Math.ceil(enclosuresNeeded - enclosures)

            enclosures = enclosures + enclosureDiff
            enclosuresBought = enclosuresBought + enclosureDiff
          }
        }

        const additionalWattageCalc = 1.12
        const voltage = 110
        const wattage = cardCount * this.currentCard.wattage * additionalWattageCalc
        // The 12% additional is to attempt to account for the fact that PSUs pull more than they provide
        const kwhTotalMonth = (wattage * 24 * 30) / 1000

        data[(i + 1)] = {
          profit: profitThisCycle,
          cardsToBuy: cardsToBuy,
          totalCards: cardCount,
          wattageTotal: wattage,
          ampTotal: wattage / voltage,
          kwhTotalMonth: kwhTotalMonth,
          taxes: this.round(taxes),
          incomePerYear: (cardCount * this.currentCard.profit[this.selectedPrice] * 365),
          incomePerMonth: (cardCount * this.currentCard.profit[this.selectedPrice] * 30),
          taxesTotal: this.round(taxesTotal),
          rigsTotal: enclosures,
          rigsToBuy: enclosuresBought
        }
      }
      return data
    },
    startingGPUCount () {
      return this.currentCard.startingCards
    }
  },
  data () {
    return {
      selectedCard: 'AMD RX 580 [8gb]',
      selectedPrice: 'med',
      includeBuyingEnclosures: false,
      electricCost: 0.11,

      cards: [
        'AMD RX 580 [8gb]',
        'Nvidia RTX 3070',
        'Nvidia RTX 3080'
      ],
      profits: [
        'low',
        'med',
        'high'
      ],
      rates: {
        'AMD RX 580 [8gb]': {
          profit: {
            low: 2.76,
            med: 3.10,
            high: 3.56
          },
          cost: 475,
          startingCards: 12,
          wattage: 130
        },
        'Nvidia RTX 3070': {
          profit: {
            low: 5.76,
            med: 6.29,
            high: 7.24
          },
          cost: 1100,
          startingCards: 6,
          wattage: 130
        },
        'Nvidia RTX 3080': {
          profit: {
            low: 9.20,
            med: 10.11,
            high: 11.50
          },
          cost: 2000,
          startingCards: 4,
          wattage: 240
        }
      },

      components: {
        motherboard: {
          price: 250,
          gpusSeated: 13,
          required: 1
        },
        psu: {
          price: 180,
          required: 2
        },
        ram: {
          price: 140,
          required: 1
        },
        cpu: {
          price: 93,
          required: 1
        },
        ssd: {
          price: 30.5,
          required: 1
        },
        risers: {
          price: 5,
          required: 13
        },
        frame: {
          price: 120,
          required: 1
        }
      }
    }
  },
  methods: {
    round (val) {
      return Math.ceil(val * 100) / 100
    }
  }
}
</script>
<style scoped>
.table-heading {
  font-size: 22px;
  font-weight: bold;
}
</style>
