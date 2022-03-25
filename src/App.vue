<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        Vue longterm nicehash profitability calculator
      </div>

      <v-spacer></v-spacer>
    </v-app-bar>

    <v-main>
      <v-dialog v-model="showWarning" max-width="600">
        <v-card>
          <v-card-title>
            Warning!
          </v-card-title>
          <v-card-text>
            This project was originally written as a proof of concept in late 2020. As such none of the figures are accurate with today's actual figures, so take the settings with a grain of salt. I would also like to call out that this was only built as a proof of concept (when these calculations were too hard to do in excel) and as such the code isn't the cleanest or most well documented. With that being said: Have fun poking around!
          </v-card-text>
          <v-card-actions>
            <v-row dense>
              <v-col cols="12">
                <v-btn block color="primary" @click="showWarning = !showWarning">
                  Dismiss
                </v-btn>
              </v-col>
            </v-row>
          </v-card-actions>
        </v-card>
      </v-dialog>
      <router-view />
    </v-main>
  </v-app>
</template>

<script>

export default {
  name: 'App',

  data: () => ({
    showWarning: true
  })
}
</script>
