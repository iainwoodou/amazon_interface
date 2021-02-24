<template>
 <v-container style="max-width:1000px;"  >
  
<div v-if="!gotdata">
    <h4 style="font-weight: normal">Please Wait ... getting machine information</h4>
      <v-progress-linear
        height="35"
        indeterminate
         align-center
      ></v-progress-linear>
</div>

<div v-else>     
       
  <h2 style="font-weight: normal">Available machines</h2>
     <p>
These are your available virtual machines .... blah blah blah ...... more info if needed click start to start ... you can figure it out from there
          </p>
         <v-row >
        
        <v-col cols="12" sm="4"
        v-for="(available, index) in availableImages" :key="index"
        >
 <v-card  >
  
    <v-card-text style="text-align: center;">
    
    <v-icon style="font-size:10rem" v-if="!available.state"  color="error">mdi-cloud-circle</v-icon>
    <v-icon style="font-size:10rem" v-if="available.state === 'loading'"  color="warning">mdi-cloud-circle</v-icon>
    <v-icon style="font-size:10rem" v-if="available.state === 'running'"  color="success">mdi-cloud-circle</v-icon>
    </v-card-text>

    <v-card-title>{{available.name}}</v-card-title>
    <v-card-text>
      <v-chip-group >
        <v-chip   class="">{{available.os}}</v-chip>
        <v-chip   class="">{{available.type}}</v-chip>
        <v-chip   class="">{{available.port}}</v-chip>   
      </v-chip-group>
    </v-card-text>


  
    <v-divider class="mx-4"></v-divider>

    <v-card-actions>
        <v-progress-linear
        height="35"
        indeterminate
         v-if="available.state ==='loading'"
         align-center
      ></v-progress-linear>

      <v-btn color="success" block @click="startMachine(index)" v-if="!available.state">
      Start <v-icon right dark>mdi-cloud-refresh</v-icon> 
      </v-btn>
    
         <v-btn color="error" v-if="available.state==='running'" @click="stopMachine(index)">
        Stop <v-icon right dark>mdi-cloud-off-outline</v-icon> 
      </v-btn>

        <v-spacer></v-spacer>
     <v-btn color="primary" v-if="available.state==='running'"
     @click="showDialog(index)" >
        View <v-icon right dark>mdi-eye</v-icon> 
      </v-btn>

    </v-card-actions>
  </v-card>
        </v-col>
      </v-row>
      

 <v-dialog
      v-model="dialog"
        transition="dialog-top-transition"
      max-width="600"
    >
      <v-card>
        <v-card-title class="headline">
          Log into {{selectedInfo.name}}
        </v-card-title>
        <v-card-text>
          <div>
          Username : waltonhall
          </div>
              <div>
          Password : MyP@55word!
          </div>
        </v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>

          <v-btn
            color="green darken-1"
            text
            @click="dialog = false"
          >
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
    name: 'HelloWorld',
    data(){
      return {
        loading : true,
        availableImages : [],
        dialog : false,
        selectedInfo : {},
        gotdata : false
      }
    },
    methods:{
      startMachine(i){
      this.availableImages[i].state =  'loading'
let x= setInterval(()=>{
  this.availableImages[i].state ='running'
  clearInterval(x)
},3000) 
      },
            stopMachine(i){
      this.availableImages[i].state =  'loading'
let x= setInterval(()=>{
  this.availableImages[i].state =null
  clearInterval(x)
},3000) 
      },
      showDialog(i){
        this.selectedInfo = this.availableImages[i]
        this.dialog=true
      }
    },
      mounted() {
        
    // get the  available machines list
    // fect a list from the API here
    this.availableImages = [
      {
        name: "Jupyter Notebook",
        os: "linux",
        type: "EC2",
        port: "8080",
        state : false
      },
      {
        name: "AI Training (non GPU)",
        os: "linux",
        type: "EC2",
        port: "8080",
           state : false
      },

      {
        name: "AI Training (With GPU)",
        os: "linux",
        type: "EC2",
        port: "8080",
           state : false
      }  

    ];

    let x= setInterval(()=>{
  this.gotdata =true
  clearInterval(x)
},3000) 
  },
  }
</script>
