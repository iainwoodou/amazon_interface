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
        <div style="text-align: center">
          <svg
            style="height: 100px; margin: 20px"
            version="1.1"
            id="L1"
            xmlns="http://www.w3.org/2000/svg"
            xmlns:xlink="http://www.w3.org/1999/xlink"
            x="0px"
            y="0px"
            viewBox="0 0 100 100"
            enable-background="new 0 0 100 100"
            xml:space="preserve"
          >
            <circle
              fill="none"
              stroke="#ccc"
              stroke-width="6"
              stroke-miterlimit="15"
              stroke-dasharray="14.2472,14.2472"
              cx="50"
              cy="50"
              r="47"
            >
              <animateTransform
                attributeName="transform"
                attributeType="XML"
                type="rotate"
                dur="5s"
                from="0 50 50"
                to="360 50 50"
                repeatCount="indefinite"
              />
            </circle>
            <circle
              fill="none"
              stroke="#ccc"
              stroke-width="1"
              stroke-miterlimit="10"
              stroke-dasharray="10,10"
              cx="50"
              cy="50"
              r="39"
            >
              <animateTransform
                attributeName="transform"
                attributeType="XML"
                type="rotate"
                dur="5s"
                from="0 50 50"
                to="-360 50 50"
                repeatCount="indefinite"
              />
            </circle>
            <g fill="#ccc">
              <rect x="30" y="35" width="5" height="30">
                <animateTransform
                  attributeName="transform"
                  dur="1s"
                  type="translate"
                  values="0 5 ; 0 -5; 0 5"
                  repeatCount="indefinite"
                  begin="0.1"
                />
              </rect>
              <rect x="40" y="35" width="5" height="30">
                <animateTransform
                  attributeName="transform"
                  dur="1s"
                  type="translate"
                  values="0 5 ; 0 -5; 0 5"
                  repeatCount="indefinite"
                  begin="0.2"
                />
              </rect>
              <rect x="50" y="35" width="5" height="30">
                <animateTransform
                  attributeName="transform"
                  dur="1s"
                  type="translate"
                  values="0 5 ; 0 -5; 0 5"
                  repeatCount="indefinite"
                  begin="0.3"
                />
              </rect>
              <rect x="60" y="35" width="5" height="30">
                <animateTransform
                  attributeName="transform"
                  dur="1s"
                  type="translate"
                  values="0 5 ; 0 -5; 0 5"
                  repeatCount="indefinite"
                  begin="0.4"
                />
              </rect>
              <rect x="70" y="35" width="5" height="30">
                <animateTransform
                  attributeName="transform"
                  dur="1s"
                  type="translate"
                  values="0 5 ; 0 -5; 0 5"
                  repeatCount="indefinite"
                  begin="0.5"
                />
              </rect>
            </g>
          </svg>
        </div>
        <h1 style="text-align: center">
          Currently Provisioning a Machine please wait
        </h1>
        <div style="max-width: 70%; margin: 0 auto; text-align: center">
          <p>Your machine is starting up, it will appear shortly.</p>
          <v-btn color="error" @click="stopMachine(startingmachine)">
            Stop <v-icon right dark>mdi-cloud-off-outline</v-icon>
          </v-btn>

          <v-progress-linear
            height="35"
            indeterminate
            align-center
          ></v-progress-linear>
        </div>
      </div>

      <div v-if="!startingmachine">
        <h2 style="font-weight: normal">Available machines</h2>
        <p>These are your available virtual machines.</p>

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
                  v-if="!machines[available.name.toLowerCase()]"
                  style="font-size: 10rem"
                  >mdi-cloud-circle</v-icon
                >
                <v-icon
                  v-if="machines[available.name.toLowerCase()]"
                  color="success"
                  style="font-size: 10rem"
                  >mdi-check-circle</v-icon
                >
              </v-card-text>

              <v-card-title>{{ available["name"] }}</v-card-title>
              <v-card-subtitle v-if="machines[available.name.toLowerCase()]">
                <strong
                  >{{ machines[available.name.toLowerCase()].state }}
                </strong>
                <BR />
                {{ machines[available.name.toLowerCase()].started }}
              </v-card-subtitle>
              <v-card-text>
                <v-chip-group>
                  <v-chip class="">{{ available["OS"] }}</v-chip>
                  <v-chip class="">{{ available["Type"] }}</v-chip>
                  <v-chip class="">{{ available["HTTPPort"] }}</v-chip>
                </v-chip-group>
              </v-card-text>

              <v-divider class="mx-4"></v-divider>
              <v-card-actions>
                <v-progress-linear
                  height="35"
                  indeterminate
                  v-if="!machines[available.name.toLowerCase()] && !allowstart"
                  align-center
                ></v-progress-linear>

                <v-btn
                  color="success"
                  v-if="!machines[available.name.toLowerCase()] && allowstart"
                  @click="startMachine(available.name)"
                >
                  Start <v-icon right dark>mdi-cloud-refresh</v-icon>
                </v-btn>
                <v-btn
                  color="error"
                  v-if="machines[available.name.toLowerCase()]"
                  @click="stopMachine(machines[available.name.toLowerCase()])"
                >
                  Stop <v-icon right dark>mdi-cloud-off-outline</v-icon>
                </v-btn>

                <v-spacer></v-spacer>
                <v-btn
                  color="primary"
                  v-if="machines[available.name.toLowerCase()]"
                  @click="showDialog(machines[available.name.toLowerCase()])"
                >
                  View <v-icon right dark>mdi-eye</v-icon>
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-col>
        </v-row>
        <v-row v-if="machines.length">
          <v-col cols="12">
            Use the file manager to Upload and Download files from your
            containers <br />
            <v-btn color="primary" href="/filemanager/index.php">
              <v-icon class="mr-4">mdi-folder</v-icon> File Manager
            </v-btn>
          </v-col>
        </v-row>
      </div>
      <v-dialog
        v-model="dialog"
        transition="dialog-top-transition"
        max-width="600"
      >
        <v-card>
          <div v-if="!dialoginfo">
            <v-card-title class="headline"> Loading information </v-card-title>
            <v-card-text>
              We are getting your credentials for the machine.
            </v-card-text>
            <v-progress-linear
              height="35"
              indeterminate
              align-center
            ></v-progress-linear>
          </div>
          <div v-else>
            <v-card-title class="headline">
              Log into {{ dialoginfo.name }}
            </v-card-title>
            <v-card-text>
              <p>For initial login:</p>
              <p>UserName: <strong>student</strong></p>
              <p>
                Password: <strong>{{ dialoginfo.password }}</strong>
              </p>

              <p>
                For Jupyter Notesbooks please then use the following password.
              </p>
              <p>Password: <strong>WaltonHall</strong></p>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>

              <v-btn disabled text v-if="dialoginfo.state != 'ready'">
                Please Wait .... connecting
              </v-btn>

              <v-btn
                color="green darken-1"
                :href="'https://' + dialoginfo.containerfqdn"
                text
                v-if="dialoginfo.state === 'ready'"
              >
                Click here to connect
              </v-btn>
            </v-card-actions>
          </div>
        </v-card>
      </v-dialog>
    </div>

    <pre style="display: none">
{{machines}}
---------------------------------------------------------------------------- <br />
{{availableImages}}
---------------------------------------------------------------------------- <br />
{{runningContainers}}

     </pre>
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
      dialoginfo: null,
      allowstart: true,
    };
  },
  methods: {
    startMachine(i) {
      this.allowstart = false;
      console.log("Starting machine: ", i);
      let postdata = new FormData();
      postdata.append("action", "startcontainer");
      postdata.append("container", i);
      fetch("aws-service.php", {
        method: "POST",
        body: postdata,
      })
        .then((res) => {
          console.log(res);
          return res.json();
        })
        .then((data) => {
          console.log(data);
          this.updateJson();
        });
    },
    stopMachine(i) {
      console.log("Stopping machine: ", i);
      let postdata = new FormData();
      postdata.append("action", "stopcontainer");
      postdata.append("container", i.ref);
      postdata.append("cluster", i.cluster);
      fetch("aws-service.php", {
        method: "POST",
        body: postdata,
      })
        .then((res) => {
          console.log(res);
          return res.json();
        })
        .then((data) => {
          console.log(data);
          this.updateJson();
        });
    },
    showDialog(i) {
      console.log("view ", i);
      this.dialoginfo = null;
      console.log("Viewing machine: ", i.ref);

      /*
      let postdata = new FormData();
      postdata.append("action", "viewcontainer");
      postdata.append("container", i.ref);
     fetch("aws-service.php", {
        method: "POST",
        body: postdata,
      })
*/

      var url = "aws-service.php?action=viewcontainer&container=" + i.ref;
      console.log(url);
      fetch(url)
        .then((res) => {
          console.log(res);
          return res.json();
        })
        .then((data) => {
          console.log(data);
          this.dialoginfo = data;
          if (data.state !== "ready") {
            setTimeout(this.showDialog(i), 3000);
          }
        });
      this.dialog = true;
    },
    updateJson() {
      //   fetch("data/start.json")
      fetch("aws-service.php")
        .then((res) => {
          console.log(res);
          return res.json();
        })
        .then((data) => {
          console.log(data);
          let result = data;
          this.availableImages = result.availableimages;
          this.runningContainers = result.runningcontainers;
          let obj = {};
          /*
          Loop through the running machines, if one does not have a name (is null) then prevent any new machine creation until it is named
          set a callback function
          */
          this.startingmachine = false;
          console.log("looping running containers");
          if (this.runningContainers.length) {
            this.runningContainers.forEach((element) => {
              if (element.name) {
                obj[element.name.toLowerCase()] = element;
                if(element.state!=="ready"){
                  setTimeout(this.updateJson, 5000);
                }
              } else {
                this.startingmachine = element;
                setTimeout(this.updateJson, 5000);
              }
            });
          }
          console.log("leaving update");
          this.machines = obj;
          this.gotdata = true;
          this.allowstart = true;
        });
    },
  },
  mounted() {
    this.updateJson();
  },
};
</script>
