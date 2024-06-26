<script>
//We import the diferent components used in the form
import FormInputTxtAndNum from "../components/formInputTxtAndNum.vue";
import FormInputTextArea from "../components/formInputTextArea.vue";
import FormInputDate from "../components/formInputDate.vue";

export default {
  name: "CreateEventView",
  components: { FormInputTxtAndNum, FormInputTextArea, FormInputDate },
  data() {
    return {
      eventTitle: "",
      eventDescription: "",
      eventStartDate: "",
      eventEndDate: "",
      eventLocation: "",
      eventImage: "https://i.imgur.com/YtopGYz.jpeg",
      eventType: "",
      eventCapacity: 0,
    };
  },
  mounted() {
    // We show the aside and the header
    this.$root.$data.show.aside = false;
  },
  methods: {
    //Function used to go to the home page
    goToHome() {
      this.$router.push("/");
    },
    //Function used to create an event
    createEvent() {
      //We get the token of the current user
      const token = localStorage.getItem("token");

      //We check if the information that was entered in the fields is valid
      const validInformation = this.checkFields();

      //Only if the information is valid we do the request
      if (validInformation) {
        fetch("http://puigmal.salle.url.edu/api/v2/events", {
          method: "POST",
          headers: {
            Authorization: "Bearer " + JSON.parse(token).accessToken,
            "Content-Type": "application/json",
          },
          body: JSON.stringify({
            name: this.eventTitle,
            image: this.eventImage,
            location: this.eventLocation,
            latitude: null,
            longitude: null,
            description: this.eventDescription,
            eventStart_date: this.eventStartDate,
            eventEnd_date: this.eventEndDate,
            n_participators: this.eventCapacity,
            type: this.eventType,
          }),
        })
          .then((res) => res.json())
          .then(() => {
            /*
            Instead of going to the eventDetails view when an event is created, we will go to the homeView because there is no way of 
            knowing the id that the event has when it is created (and we need the event ID so that if the user presses the "participate"
            button, we can send to the API the event ID to create the assistance of the authenticated user
            */
            this.$router.push("/");
          });
      }
    },
    checkFields() {
      if (
        this.eventTitle == "" ||
        this.eventImage == "" ||
        this.eventLocation == "" ||
        this.eventDescription == "" ||
        this.eventType == ""
      ) {
        alert("Some fields have not been filled");
        return false;
      }

      if (this.eventCapacity < 1) {
        alert("The capacity for the event must be at least 1");
        return false;
      }

      if (this.eventStartDate == this.eventEndDate) {
        alert("The start and end dates must be different");
        return false;
      }

      if (this.eventStartDate > this.eventEndDate) {
        alert("The start date or the end date are not valid");
        return false;
      }

      return true;
    },
  },
};
</script>

<template>
  <div id="create-event-main">
    <section id="title-container">
      <h2>Create event</h2>
    </section>

    <section id="forms-container">
      <div class="single-form">
        <FormInputTxtAndNum
          v-on:update-modelValue="(x) => (this.eventTitle = x)"
          title="Title"
          defaultTxt="Ex.- House BBQ"
        />
      </div>

      <div class="single-form">
        <FormInputTextArea
          v-on:update-modelValue="(x) => (this.eventDescription = x)"
          title="Description"
          defaultTxt="Ex.- This event is fun!"
        />
      </div>

      <div class="dual-form">
        <div class="single-form">
          <FormInputDate
            v-on:update-modelValue="(x) => (this.eventStartDate = x)"
            title="Start date"
          />
        </div>
        <div class="single-form">
          <FormInputDate
            v-on:update-modelValue="(x) => (this.eventEndDate = x)"
            title="End date"
          />
        </div>
      </div>

      <div class="single-form">
        <FormInputTxtAndNum
          v-on:update-modelValue="(x) => (this.eventLocation = x)"
          title="Location"
          defaultTxt="Ex.- Teruel"
        />
      </div>

      <div class="single-form">
        <FormInputTxtAndNum
          v-on:update-modelValue="(x) => (this.eventImage = x)"
          title="Image"
          defaultTxt="Ex.- www.url-of-current-img.com"
        />
      </div>

      <div class="dual-form">
        <div class="single-form">
          <FormInputTxtAndNum
            v-on:update-modelValue="(x) => (this.eventType = x)"
            title="Type"
            defaultTxt="Ex.- Sports"
          />
        </div>
        <div class="single-form">
          <FormInputTxtAndNum
            v-on:update-modelValue="(x) => (this.eventCapacity = x)"
            title="Capacity"
            defaultTxt="Ex.- 50"
            fieldType="number"
          />
        </div>
      </div>

      <div class="button-container">
        <button
          v-on:click="createEvent()"
          class="create-event-button primary-button"
        >
          CREATE EVENT
        </button>
        <button v-on:click="goToHome()" class="cancel-button secondary-button">
          CANCEL
        </button>
      </div>
    </section>
  </div>
</template>

<style scoped>
#create-event-main {
  width: 100%;
  height: 100%;
  flex-direction: column;
  display: flex;
}

#title-container {
  margin-top: 30px;
  margin-bottom: 20px;
  text-align: center;
  font-size: 15px;
  font-family: "Franklin Gothic Medium", "Arial Narrow", Arial, sans-serif;
}

#forms-container {
  display: flex;
  flex-direction: column;
  align-self: center;
  width: 70%;
  padding: 15px;
  background-color: #eeeeff;
  border-radius: 10px;
  margin-bottom: 50px;
}

.single-form {
  display: flex;
  flex-direction: column;
  margin: 5px;
}

.dual-form {
  display: flex;
  flex-flow: row wrap;
}

.dual-form div {
  flex-grow: 1;
}

.textarea {
  resize: none;
}

.button-container {
  margin: 10px;
  display: flex;
  flex-flow: row wrap;
  justify-content: space-evenly;
}

.create-event-button {
  height: 50px;
  width: 170px;
  margin: 10px;
}

.cancel-button {
  height: 50px;
  width: 170px;
  margin: 10px;
}

@media (min-width: 768px) {
  #forms-container {
    width: 40%;
    padding: 30px;
  }

  .button-container {
    margin: 30px;
  }
}
</style>
