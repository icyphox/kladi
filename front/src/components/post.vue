<template>
  <div>
    <v-app>
      <v-layout row wrap>
      <v-flex offset-lg2 lg8>
      <v-container color="blue-grey lighten-4" mt-4 mb-4 class="box">
        <!-- <h1 class="heading">UPLOAD YOUR CONTENTS</h1> -->
        <v-layout row wrap v-if="!imageURL">
          <!-- <v-flex offset-lg3 lg3>
    <v-btn raised class="button" style="background:#666; color: white; " @click="onPickFile">UPLOAD</v-btn>
    </v-flex> -->
    <v-flex offset-lg3 lg6>
   <div >
   <v-btn mb-0 large raised color="teal darken-4" round  class="button" style="background:#666; color: white; " @click="onPickFile">Upload</v-btn>
    <input type="file" id="attachments" style="display: none;" ref="file" class="input-file" @change="uploadFieldChange($event)">
   
    </div>
  </v-flex>
  </v-layout>
  <v-layout v-else row wrap >
    <v-flex offset-lg3 lg6>
       <img :src="imageURL" width="30%" height="150">
    </v-flex>
 

    </v-layout>
    <v-layout row wrap>
      <v-flex offset-lg3 lg6>
        <div v-if="flag" class="remove">
          <span>{{ files.name + ' (' + Number((files.size / 1024 / 1024).toFixed(1)) + 'MB)'}}</span>
          <span @click="removeAttachment($event)"><v-btn flat depressed small fab><v-icon>delete_forever</v-icon></v-btn></span>
        </div>

      </v-flex>
    </v-layout>
    <v-layout row wrap>
       <v-flex offset-lg3 lg6>
          <v-text-field  v-model="caption" label="Title" ></v-text-field>
       </v-flex>
    </v-layout>
    <v-layout row wrap>
       <v-flex offset-lg3 lg6>
        <v-text-field color="teal darken-4" box  v-model="des" label="Description" textarea></v-text-field>
      
       </v-flex>
    </v-layout>
    <v-layout row wrap>
       <v-flex offset-lg3 lg6>
        <v-select :items="items" color="teal darken-4" outline no-data-text="No nodes" hint="Tags (max 3)" persistent-hint ref="tags" v-model="chips" autocomplete chips clearable multiple deletable-chips hide-selected>TAGS</v-select>
      
       </v-flex>
    </v-layout>
    <v-radio-group  v-model="profileLink">
    <v-layout row wrap mt-3>
      <v-flex offset-lg3 lg1>
        <v-radio color="teal darken-4" value="anonym" class="radio" name="profile"></v-radio>
      </v-flex>
      <v-flex lg4  text-xs-left>
        <p class="options">Upload anonymously</p>
      </v-flex>
    </v-layout>
    <v-layout row wrap mt-2>
      <v-flex offset-lg3 lg1>
        <v-radio value="identifier" color="teal darken-4" class="radio" name="profile"></v-radio>
      </v-flex>
      <v-flex lg4  text-xs-left>
        <p  class="options">Identify yourself</p>
      </v-flex>
    </v-layout>
    </v-radio-group>
      <v-layout row wrap mt-2 v-if="profileLink=='identifier'">
        <v-flex offset-lg3 lg6>
        <v-text-field color="teal darken-4" box label="Name">

        </v-text-field>
        </v-flex>
       
      </v-layout>
      <v-layout row wrap style="margin-top: -3.5%;" v-if="profileLink=='identifier'">
        <v-flex offset-lg3 lg6>
        <v-text-field color="teal darken-4" box label="Profile Link">

        </v-text-field>
        </v-flex>
       
      </v-layout>
    <v-layout row wrap>
      <v-flex offset-lg3 lg6>
      <v-btn  raised large round  color="teal darken-4" class="button" style="background:#666; color: white;" @click="submit">Submit</v-btn>
    </v-flex>
    </v-layout>
  </v-container>
  </v-flex>
  </v-layout>
  </v-app>
  </div>
</template>
<script>
  import axios from 'axios'
 
  export default {
   
    name: 'post',
    data () {
      return {
        data: new FormData(),
        profile: true,
        profileLink: "anonym",
        caption: "",
        des: "",
        files: '',
        chips: [],
        errors: [],
        posts: null,
        imageURL: "",
         
         tags: [],
         items: [
           'Sports',
           'Education',
           'Technology',
           'Politics',
           'Philosophy',
           'Science',
           'Religion'
           
         ],
          customFilter(item, queryText, itemText) {
              const hasValue = val => val != null ? val : ''
              const text = hasValue(item.name)
              const query = hasValue(queryText)
              return text.toString()
                .toLowerCase()
                .indexOf(query.toString().toLowerCase()) > -1
            },
         attachments: null,
        
         flag: 0
      
        //  content: null

      }
    },
    

    methods: {
      
      onPickFile() {
         
         this.$refs.file.click()

      },
      uploadFieldChange (e) {
        const fileReader = new FileReader()
        fileReader.onload =  (e) => {
          this.imageURL =  e.target.result;
          console.log(this.imageURL)
        };
        console.log(this.files)
        this.flag = 1;
        this.files = e.target.files[0];
      },
      removeAttachment(e) {
        console.log(e)
      this.files = null;
      this.imageURL = "";
      this.$refs.file.value = null;
      this.flag = 0;
      
      },
      submit () {


        this.attachments = {
          files: this.files,
          profile: this.profileLink,
          title: this.caption,
          
          des: this.des,
          tags: this.chips
        }
        console.log(this.attachments)

        
        // console.log(this.attachments)
            // for (const key of Object.keys(this.attachments)) {
                // console.log(key, this.form[key])
                this.data.append("myfile", this.files);
                this.data.append("des", this.attachments.des);
                this.data.append("tags",JSON.stringify(this.chips));

                // console.log(data)NPM RUN DEV;
              // }
              console.log(this.data)

        //post request to icy's node on cloud 
        axios.post('http://localhost:5000/api/upload', this.data,{headers:{'Content-Type':'multipart/form-data'}})
        .then(response => {
          console.log('response...')
          console.log(response)
        })
        .catch()
          
      }

    },
    created() {
        axios.get(`http://localhost:5000/api/nodes/links`)
          .then(response => {
            // JSON responses are automatically parsed.
            this.posts = response.data;
            console.log(this.posts)
            //receiving string, convert to array
          })
          .catch(e => {
            this.errors.push(e)
          })
      }
    
  
  }

</script>

<style scoped>
   .button{
    /* width:80px;
    height:30px; */
    margin-top:2%;
    font-size: 16px;
  
  }
  .box{
    /* border: 2px solid #37474F; */
     border-radius: 10px;
     /* -webkit-box-shadow: -1px 1px 6px 0px rgba(0,77,64,1);
-moz-box-shadow: -1px 1px 6px 0px rgba(0,77,64,1);
box-shadow: -1px 1px 6px 0px rgba(0,77,64,1); */
 background: #E3F2FD;
 /* opacity: 0.85; */

  }
  #app{
  background: white;
  font-family: ;
  }
  .heading{
    margin-top: 2%;
  }

  .remove{
    margin: 1%;
    font-size: 16px;
    /* font-family: 'helvetica'; */
  }
  .remove span:last-child{
    margin-left: 1%;
    font-size: 22px;
  }

  .options{
    font-size: 16px;
    /* font-weight: 600; */

  }

  .radio{
    font-size: 22px;

  }

  
</style>