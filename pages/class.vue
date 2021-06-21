<template>
<div>
<section class="text-gray-600 body-font relative" style="min-height:90vh;" v-if="class_id">
  <div class="container px-5 py-24 mx-auto">
    <div class="flex flex-col text-center w-full">
      <div class="p-4 lg:w-full">
        <div class="h-full bg-gray-100 bg-opacity-75 px-8 pt-16 pb-24 rounded-lg overflow-hidden text-center relative">
          <h1 class="title-font sm:text-2xl text-xl font-medium text-gray-900 mb-3">Class: {{class_name}}</h1>
          <h2 class="title-font sm:text-1xl text-xl font-medium text-gray-900 mb-3">Teacher: {{class_teacher}}</h2>
          <p class="leading-relaxed mb-3">Class Time: {{new Date(class_date).toLocaleDateString("en-GB") == "Invalid Date" ? "No Date" : new Date(class_date).toLocaleDateString("en-GB")}}</p>
        </div>
      </div>
    </div>
    <div class="lg:w-1/2 md:w-2/3 mx-auto">
      <div class="flex flex-wrap ">
        <div class="p-2 w-full">
          <p class="leading-relaxed mb-3">Please provide your full name in the box below to attend the class, also be noted that to provide correct name otherwise attendance will be lost</p>
          <div class="relative">
            <label for="name" class="leading-7 text-sm text-gray-600">Name</label>
            <input type="text" id="name" name="name" v-model=" student_name" class="w-full bg-gray-100 bg-opacity-50 rounded border border-gray-300 focus:border-indigo-500 focus:bg-white focus:ring-2 focus:ring-indigo-200 text-base outline-none text-gray-700 py-1 px-3 leading-8 transition-colors duration-200 ease-in-out">
          </div>
        </div>
        <div class="p-2 w-full">
          <button class="flex mx-auto text-white bg-indigo-500 border-0 py-2 px-8 focus:outline-none hover:bg-indigo-600 rounded text-lg" @click="attend_class">Submit</button>
        </div>
      </div>
    </div>
  </div>
</section>
<section class="text-gray-600 body-font" style="min-height:90vh;" v-else>
  <div class="container px-5 py-24 mx-auto">
    <div class="lg:w-2/3 flex flex-col sm:flex-row sm:items-center items-start mx-auto">
      <h1 class="flex-grow sm:pr-16 text-2xl font-medium title-font text-gray-900">Loading Class Details</h1>
      <a class="flex-shrink-0 text-white bg-indigo-500 border-0 py-2 px-8 focus:outline-none hover:bg-indigo-600 rounded text-lg mt-10 sm:mt-0" href="/">Home</a>
    </div>
  </div>
</section>
</div>
</template>

<script>
export default {
  layout:'class',
  data() {
    return {
      class_id:null,
      class_name:"",
      class_teacher:"",
      class_url:"",
      student_name:"",
      class_db_id:"",
      class_date:""
    }
  },
  methods: {
    async attend_class(){
      if(this.student_name == "" || this.student_name == null)
      {
        alert("please provide your name !");
        return;
      }

      var attend = await this.$axios.post("/api/attend/"+this.class_db_id+"/name/"+this.student_name);

      if(attend.status == 200){
        window.location.href = this.class_url;
      }


    }
  },
  async created() {
    var class_id = this.$route.query.class_id;

try{
var class_data = await this.$axios.get("/api/class/"+class_id);
console.log(class_data.data)

   if(class_data.status == 200){
     this.class_name = class_data.data.class_name;
     this.class_teacher = class_data.data.class_teacher;
     this.class_url = class_data.data.class_url;
     this.class_db_id = class_data.data._id;
     this.class_date = class_data.data.class_date;
     this.class_id = true;
   }else{
     this.class_id = false;
   }


}catch{
this.class_id =false;
}

  },
}
</script>
