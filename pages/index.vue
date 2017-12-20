<template>
  <div class="container">
    <h1>Room List</h1>
    <br>
    <br>
    <div class="room-list">
      <nuxt-link class="room" :to="'/room/'+room.id"  v-for="room in rooms">
        <span class="room__text">{{ room.id }}</span>
        <span class="room__text">{{ room.name }}</span>
      </nuxt-link>
    </div>
  </div>
</template>

<script>

import http from "axios";

export default {
  async asyncData(){
    let { data } = await http.get(`http://localhost:9000/rooms`)
    return {
      rooms : data
    }
  }
}
</script>

<style lang="scss" scoped>
.container {
  max-width: 1280px;
  min-height: 100vh;
  flex-direction: column;
  margin: auto;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.room-list{
  display: flex;
  flex-direction: column;
}

.room{
  display: flex;
  padding: 16px;
  margin: 8px 0;
  border: 1px solid lightgrey;
  text-decoration: none;
  color: darkslategrey;
  
  &:hover{
    background: whitesmoke;
    transition: 0.5s ease;
  }

  &__text{
    margin: 0 16px;
  }
}

</style>
