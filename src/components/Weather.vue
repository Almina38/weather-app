<template>
  <div class="container p-0">
    <div class="d-flex">
      <div class="card main-div w-100">
        <div class="p-3">
          <h2 class="mb-1 day">Tuesday</h2>
          <p class="text light date mb-0">date</p>
          <small>time</small>
          <h2 class="place">
            <i class="fa fa-location"></i> Rio <small>country</small>
          </h2>
          <div class="temp">
            <h1 class="weather-temp">19&nbsp;&deg;</h1>
            <h2 class="text-light">description</h2>
          </div>
        </div>
      </div>
    </div>

    <div class="card card-2 w-100">
      <table class="m-4">
        <tbody>
          <tr>
            <th>Sea Level</th>
            <td>100</td>
          </tr>
          <tr>
            <th>Sea Level</th>
            <td>100</td>
          </tr>
          <tr>
            <th>Sea Level</th>
            <td>100</td>
          </tr>
        </tbody>
      </table>

      <div id="div_Form" class="d-flex m-3 justify-content-center">
        <form action="">
          <input type="button" value="Change Location" class="btn change-btn btn-primary">
        </form>
      </div>
    </div>
  </div>
</template>

<script>
export default (await import('vue')).defineComponent({
  name: 'myWeather',
  components: {
  
  },
})
</script>

<style >
body{
  background-color: #343d4b;
}

.weather-temp{
  margin: 0;
  font-weight: 700;
  font-size: 4em;
}

h2.mb-1.day{
  font-size: 3rem;
  font-weight: 400;
}

.main-div{
  border-radius: 20px;
  color: #fff;
background-image: url('@/assets/img/background.png');  background-size: cover;
  background-position: center;
  background-blend-mode: overlay;
  background-color: rgb(0, 0, 0, 0.5);
  background-repeat: no-repeat;
}

.temp{
  position: absolute;
  bottom: 0;
}

.main-div:hover
{
  transform: scale(1.1);
  transition: transform 0.5s ease;
  z-index: 1;

}

.card-2{
  background-color: #212730;
  border-radius: 2px;
}

/* h2, p{
padding: 0 20px 0;
}*/ 

.card-details{
  margin-left: 19px;
}

h1._left{
  position: absolute;
  bottom: 25px;
  left: 16px;
  font-size: 3vw;
  line-height: 1.2;
}



</style>
