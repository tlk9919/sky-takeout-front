<template>
  <div class="hello">
    <input type="button" value="send request" @click="sendRequest">
  </div>

</template>

<script>
import axios  from "axios";
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  methods: {
     sendRequest() {
      axios.post('api/admin/employee/login', {
        username: 'admin',
        password: '123456',
      })
          .then(res => {
            console.log(res);
          })
          .catch(err => console.log(err));
    }
  }
}
</script>


<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
