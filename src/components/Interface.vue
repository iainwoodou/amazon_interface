<template>
  <v-container style="max-width: 1000px">
    <div v-if="!gotdata">
      <h4 style="font-weight: normal">
        Please Wait ... getting machine information
      </h4>
      <v-progress-linear
        height="35"
        indeterminate
        align-center
      ></v-progress-linear>
    </div>
    <div v-else>
      
      <div v-if="startingmachine">
        Currently Provisioning a Machine please wait
        <v-btn color="error" @click="stopMachine(startingmachine.uniqueID)">
          Stop <v-icon right dark>mdi-cloud-off-outline</v-icon>
        </v-btn>
      </div>

      <div v-if="!startingmachine">
           <h2 style="font-weight: normal">Available machines</h2>
      <p>
        These are your available virtual machines .... blah blah blah ......
        more info if needed click start to start ... you can figure it out from
        there
      </p>

      <v-row>
        <v-col
          cols="12"
          sm="4"
          v-for="(available, index) in availableImages"
          :key="index"
        >
          <v-card>
            <v-card-text style="text-align: center">
              <v-icon
                style="font-size: 10rem"
                >mdi-cloud-circle</v-icon
              >
            </v-card-text>

            <v-card-title>{{ available["name"] }}</v-card-title>
            <v-card-text>
              <v-chip-group>
                <v-chip class="">{{ available["OS"] }}</v-chip>
                <v-chip class="">{{ available["Type"] }}</v-chip>
                <v-chip class="">{{ available["HTTPPort"] }}</v-chip>
              </v-chip-group>
            </v-card-text>

            <v-divider class="mx-4"></v-divider>
            <v-card-actions>
              <v-btn
                color="success"
                
                @click="startMachine(available.Name)"
              >
                Start <v-icon right dark>mdi-cloud-refresh</v-icon>
              </v-btn>
              <v-btn
                color="error"
                v-if="available.state === 'running'"
                @click="stopMachine(index)"
              >
                Stop <v-icon right dark>mdi-cloud-off-outline</v-icon>
              </v-btn>

              <v-spacer></v-spacer>
              <v-btn
                color="primary"
                @click="showDialog(index)"
              >
                View <v-icon right dark>mdi-eye</v-icon>
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-col>
      </v-row>


<pre>
  {{runningContainers}}
</pre>
      </div>
      <v-dialog
        v-model="dialog"
        transition="dialog-top-transition"
        max-width="600"
      >
        <v-card>
          <v-card-title class="headline">
            Log into {{ selectedInfo.name }}
          </v-card-title>
          <v-card-text>
            <div>Username : waltonhall</div>
            <div>Password : MyP@55word!</div>
          </v-card-text>

          <v-card-actions>
            <v-spacer></v-spacer>

            <v-btn color="green darken-1" text @click="dialog = false">
              Go to the Machine
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </div>
  </v-container>
</template>

<script>
export default {
  name: "Interface",
  data() {
    return {
      loading: true,
      availableImages: [],
      runningContainers: [],
      dialog: false,
      selectedInfo: {},
      gotdata: false,
      machines: {},
      startingmachine: false,
    };
  },
  methods: {
    startMachine(i) {
console.log("Starting machine: ", i )
      let postdata = new FormData();
      postdata.action = "startcontainer";
      postdata.container = i;
      fetch("aws-service.php", {
         method:"POST",
          body:postdata })
        .then((res) => {
          return res.json();
        })
        .then((data) => {
          console.log(data);
          this.updateJson();
        });
    },
    stopMachine(i) {
      console.log("Stopping machine: ", i )
      let postdata = new FormData();
      postdata.action = "stopcontainer";
      postdata.container = i;
      fetch("aws-service.php", {
         method:"POST",
          body:postdata })
        .then((res) => {
          return res.json();
        })
        .then((data) => {
          console.log(data);
          this.updateJson();
        });
    },
    showDialog(i) {
      this.selectedInfo = this.availableImages[i];
      this.dialog = true;
    },
    updateJson() {
      fetch("aws-service.php")
        .then((res) => {
          return res.json();
        })
        .then((data) => {
          console.log(data);
          let result = data;
          this.availableImages = result.availableimages;
          this.runningContainers = result.runningcontainers;
          this.gotdata = true;
          let obj = {};

          /*
          Loop through the running machines, if one does not have a name (is null) then prevent any new machine creation until it is named
          set a callback function
          */
          this.startingmachine = false;
          if (this.runningContainers.length) {
            this.runningContainers.forEach((element) => {
              if (element.name) {
                obj[element.name] = element;
              } else {
                this.startingmachine = element;
                setTimeout(this.updateJson, 5000);
              }
            });
          }
          this.machines = obj;
        });
    },
  },
  mounted() {
    this.updateJson();
  },
};
</script>
