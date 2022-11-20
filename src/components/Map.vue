<script>
import { Loader } from '@googlemaps/js-api-loader';
import { ref, onMounted } from 'vue'

export default {
  name: 'Map',
  data () {
    return {
      OriginPlace: '',
      DestinationPlace: '',
      Distance: '0',
      DirectionRenderer:'',
      map: ''
    }
  },
  mounted() {
    const loader = new Loader({
      apiKey: "[Your API key]",
      version: "weekly",
      libraries: ["places"]
    });
    const mapOptions = {
      center: {
        lat: 0,
        lng: 0
      },
      zoom: 2,
      zoomControl: false,
      streetViewControl: false,
      mapTypeControl: false,
      fullscreenControl: false
    };
    loader.loadCallback(e => {
      if (e) {
        console.log(e);
      } else {
        this.map = new google.maps.Map(document.getElementById("map"), mapOptions);
      }
      this.OriginPlace = new google.maps.places.Autocomplete(
            this.$refs["origin"],
            { fields: ["place_id"] }
          );
      this.DestinationPlace = new google.maps.places.Autocomplete(
            this.$refs["destination"],
            { fields: ["place_id"] }
          );
      this.DirectionsRenderer = new google.maps.DirectionsRenderer();
      this.DirectionsRenderer.setMap(this.map);
    });
  },
  methods: {
    async CalculateRoute() {
      
      const DirectionService = new google.maps.DirectionsService();
      const OriginPlace = this.OriginPlace.getPlace()
      const DestinationPlace = this.DestinationPlace.getPlace()

      const results = await DirectionService.route(
        {
        origin: { placeId :OriginPlace.place_id},
        destination: { placeId :DestinationPlace.place_id},
        // eslint-disable-next-line no-undef
        travelMode: google.maps.TravelMode.DRIVING,
        },
        (response, status) => {
          if (status === "OK") {
            console.log(response)
            this.DirectionsRenderer.setDirections(response);
          } else {
            window.alert("Directions request failed due to " + status);
          }
        }
      )
      this.Distance = results.routes[0].legs[0].distance.text
    }
  }

}
</script>
<template>
  <div class="calculateMap">
    <input type="text" placeholder="Origin" ref="origin" />
    <input type="text" placeholder="Destination" ref="destination" />
    <button @click="CalculateRoute">Calculate</button>
    <p> Distance: <b>{{ Distance }}</b> </p>
  </div>
  <div id="map">
  </div>
  <br />
</template>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
#map {
  width: 100%;
  height: 95vh;
}
.calculateMap {
  position: absolute;
  margin-top: 1%;
  z-index: 1;
  background-color: whitesmoke;
  border-radius:10px;
  padding: 1%;
  display: flex;
  flex-wrap: wrap;

  button {
    position: relative;
    top: 0px;
    padding: 15px 25px;
    border: solid #1a1a1a 3px;
    border-radius: 20px;
    margin: 10px;
    box-shadow: 0px 10px 0px #1a1a1a;
  }
button {
  background: #ffc300;
}
button:hover {
  background: #efb300;
}
button:active {
  background: #dfa300;
}
  input {
    border: 1px solid blanchedalmond;
    border-radius: 10px;
    margin: 2%;
    height: 40px;
  }
}
</style>