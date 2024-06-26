<script>
// We define the eventsAssisted array to store the events the user have assisted
export default {
  name: "TimelineView",
  data() {
    return {
      eventsAssisted: [],
    };
  },

  methods: {
    // We get the events the user have assisted
    getEventsAssisted() {
      // We get the token from local storage and the user id
      const token = localStorage.getItem("token");
      const userID = JSON.parse(localStorage.getItem("userInfo"))[0].id;
      let URL =
        "http://puigmal.salle.url.edu/api/v2/users/" + userID + "/assistances";

      // We make the request to the API to get the events the user have assisted
      fetch(URL, {
        method: "GET",
        headers: {
          Authorization: "Bearer " + JSON.parse(token).accessToken,
          "Content-Type": "application/json",
        },
      })
        .then((res) => res.json())
        .then((res) => {
          this.eventsAssisted = res;
          // We format the date and time of the events to show it in the timeline
          res.map((event) => {
            event.date =
              event.date.split("T")[0] +
              " / " +
              event.date.split("T")[1].substring(0, 5);
          });
          // We check if the event has an image and if it has not, we put a default image
          res.map((event) => {
            if (!this.checkURL(event.image)) {
              event.image = "src/assets/default_img.png";
            }
          });
        });
    },
    // Function to check if an image is valid
    checkURL(url) {
      return url.match(/\.(jpeg|jpg|gif|png)$/) != null;
    },
  },

  created() {
    this.getEventsAssisted();
  },
};
</script>

<template>
  <section id="container-timeline">
    <ul v-for="event in eventsAssisted" v-bind:key="event.id">
      <li>
        <div class="event-timeline">
          <div class="img-date-title-timeline">
            <img
              v-bind:src="event.image"
              onerror="this.src = 'src/assets/default_img.png'"
              alt="event image"
            />
            <div class="date-title-timeline">
              <h5 class="event-datetime">{{ event.date }}</h5>
              <h4 class="event-title">{{ event.name }}</h4>
            </div>
          </div>

          <h5 class="event-location">{{ event.location }}</h5>
        </div>
      </li>
    </ul>
  </section>
</template>

<style scoped>
img {
  width: 20%;
  height: fit-content;
}

#container-timeline {
  margin: 25px;
  border-left: 5px solid #000;
  padding: 0 20px 0 30px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  flex-wrap: wrap;
}

.event-timeline {
  background-color: #fafafa;
  padding: 5px 12px;
  box-shadow: 3px 3px 10px 3px rgba(0, 0, 0, 0.2);
  border-radius: 12px;
  flex-basis: 30%;
  display: flex;
  line-height: 1.7;
  position: relative;
  margin-bottom: 10px;
  margin-top: 10px;
  flex-direction: row;
  justify-content: space-between;
  align-items: flex-end;
  flex-wrap: wrap;
}

.img-date-title-timeline {
  display: flex;
  flex-direction: row;
}

.date-title-timeline {
  padding-bottom: 7px;
  padding-top: 7px;
  padding-left: 20px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: stretch;
}

.event-timeline::before {
  content: "";
  border-radius: 50%;
  background-color: #000;
  border: 2px solid #000;
  display: block;
  width: 10px;
  height: 10px;
  position: absolute;
  top: 35px;
  left: -40px;
}

h4 {
  font-size: 0.9em;
  font-weight: bold;
}

h5 {
  font-size: 0.7em;
}

ul {
  list-style-type: none;
  margin: 0;
  padding: 0;
}

/*MEDIA QUERIES*/
@media (min-width: 768px) {
  #container-timeline {
    margin: 25px 90px;
    flex-wrap: nowrap;
  }

  .event-timeline {
    flex-wrap: nowrap;
    padding: 10px 25px;
  }

  h4 {
    font-size: default;
  }

  h5 {
    font-size: default;
  }
}
</style>
