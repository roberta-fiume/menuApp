<template>
   <div class='home'>
    <div class='home__main'>
      <div class="home__main-init"> 
        <p class="home__main-init-text">Who visisted my restaurant?</p>
        <p class="home__main-init-text">Let's find out! </p>
        <button class="home__main-init-button" @click="showCalendar"> See guests </button>
      </div>
      <FullCalendar
        class='demo-app-calendar'
        :options='calendarOptions'
        v-if="isCalendarVisible"
      >
        <template v-slot:eventContent='arg'>
          <b>{{ arg.timeText }}</b>
          <i>{{ arg.event.title }}</i>
        </template>
      </FullCalendar>
    </div>

    <Modal
      v-show="isModalVisible"
      @close="closeModal"
    >
        <template v-slot:header>
            <h3 class="home__header">Guest Info</h3>
        </template>

        <template v-slot:body>
            <div v-if="show">
              <p class="home__noGuests"> {{ noGuests }}</p>
            </div>
            <div class="home__guest" v-if="!show">
                <div class="home__guest-info">
                    <label class="home__guest-label"> Guest ID: </label>
                    <p class="home__guest-name"> {{ guest.id }} </p>
                </div>
                <div class="home__guest-info">
                    <label class="home__guest-label"> Name: </label>
                    <p class="home__guest-name"> {{ guest.title }} </p>
                </div>
                <div class="home__guest-info">
                    <label class="home__guest-label"> Email: </label>
                    <p class="home__guest-name"> {{ guest.email }} </p>
                </div>
                <div class="home__guest-info">
                    <label class="home__guest-label"> Date of visit: </label>
                    <p class="home__guest-name"> {{ guest.start }} </p>
                </div>
                <div class="home__guest-info">
                    <label class="home__guest-label"> Order: </label>
                    <p class="home__guest-name"> {{ guest.name }} </p>
                </div>
                <div class="home__guest-info">
                    <label class="home__guest-label"> Quantity: </label>
                    <p class="home__guest-name"> {{ guest.quantity }} </p>
                </div>
                <div class="home__guest-info">
                    <label class="home__guest-label"> Price: </label>
                    <p class="home__guest-name"> {{ guest.price}} </p>
                </div>
            </div>
        </template>
    </Modal>
  </div>
</template>

<script>
/* eslint-disable no-unused-vars*/ 
import FullCalendar from '@fullcalendar/vue'
import dayGridPlugin from '@fullcalendar/daygrid'
import timeGridPlugin from '@fullcalendar/timegrid'
import interactionPlugin from '@fullcalendar/interaction'
import { INITIAL_EVENTS, createEventId } from '../event-utils';
import Modal from "../components/Modal";


const axios = require('axios');

const guestsUrl = 'https://tst-api.feeditback.com/exam.guests';
const ordersUrl = 'https://tst-api.feeditback.com/exam.guests.orders'

export default {

  components: {
    FullCalendar,
    Modal, // make the <FullCalendar> tag available
  },

  data: function() {
    return {
      calendarOptions: {
        plugins: [
          dayGridPlugin,
          timeGridPlugin,
          interactionPlugin // needed for dateClick
        ],
        events: [],
        initialView: 'dayGridMonth',
        initialEvents: INITIAL_EVENTS, // alternatively, use the `events` setting to fetch from a feed
        editable: true,
        selectable: true,
        selectMirror: true,
        dayMaxEvents: true,
        weekends: true,
        select: this.handleDateSelect,
        eventClick: this.seeGuestInfo,
        eventsSet: this.handleEvents
        /* you can update a remote database when these fire:
        eventAdd:
        eventChange:
        eventRemove:
        */
      },
      guests: [],
      orders: [],
      guest: {},
      isModalVisible: false,
      show: false,
      noGuests: "There are no guests for this day",
      isCalendarVisible: false,
    }
  },

  created() {
    this.guestsUrl = guestsUrl;
    this.ordersUrl = ordersUrl;
    this.initCalendar(INITIAL_EVENTS); 
    this.select = this.eventClick;
  }, 

  mounted() {
      this.getOrders();
  },

  methods: {

    showCalendar() {
      this.isCalendarVisible = !this.isCalendarVisible;
    },

    getOrders() {
      const headers = {
        'X-FIB-API-AUTH-TYPE':'exam',
        'X-FIB-API-LANGUAGE':'en_GB',
        'X-FIB-API-AUTH-TOKEN':'F6HCAFVHPEg3"Sw#',
        'Content-Type': 'application/json'
      }
      console.log("HEADERS", headers);

      axios.get(`${ordersUrl}`, { params: { offset: 0, limit: 100 }, headers: headers }).then(response => {
        let ordersApi = response.data.items;
        console.log("RESPONSE", response.data.items);
        this.orders = ordersApi;
        console.log("ORDERS", this.orders);
      })
      .catch(error => {
        console.log("this is the error", error);
      });
    },

    handleDateSelect(clickInfo, infoGuest) {
      console.log("HANDLE DATE SELECT",clickInfo);
      let element = document.querySelector('.fc-direction-ltr .fc-daygrid-event.fc-event-end');
      console.log("INSIDE ELEMENT", element);
      if(!element) {
        console.log("ELEMENT DOESN'T EXIST");
        this.show = true;
        this.showModal(); 
      }
    },

    seeGuestInfo(infoGuest) {
      console.log("INFOGUEST", infoGuest);
      if (infoGuest.event.title) {
          console.log("single guest");
        for (var i in this.calendarOptions.events) {
          if (this.calendarOptions.events[i].title === infoGuest.event.title) {
            this.guest = this.calendarOptions.events[i];
            console.log(this.calendarOptions.events[i]);
            for (var k in this.orders) {
              console.log("GUEST ID", this.guest.id);
              if (this.guest.id === this.orders[k].guest_id) {
                this.guest.price = this.orders[k].price;
                this.guest.name = this.orders[k].name;
                this.guest.quantity = this.orders[k].quantity;
                  console.log("COMPLETE GUEST", this.guest);
              }
            }
            this.showModal();
          }
        }
      } 
    },

    handleEvents(guests) {
      this.guests = guests
    },

    initCalendar() {
        this.getGuests();
    },

    
    getGuests() {
       const headers = {
        'X-FIB-API-AUTH-TYPE':'exam',
        'X-FIB-API-LANGUAGE':'en_GB',
        'X-FIB-API-AUTH-TOKEN':'F6HCAFVHPEg3"Sw#',
        'Content-Type': 'application/json'
      }
      console.log("HEADERS", headers);

      axios.get(`${guestsUrl}`, { params: { offset: 0, limit: 100 }, headers: headers }).then(response => {
        let guests = response.data.items;
        console.log("RESPONSE", response.data.items);
        let apiGuests = guests.map(guest => this.visitingGuest(guest));
        this.calendarOptions.events = [... apiGuests];
         console.log("THIS IS CALENDAR OPTIONS EVENTS",this.calendarOptions.events)
      })
      .catch(error => {
        console.log("this is the error", error);
      });

    },

    visitingGuest(guest) { //factory function 
      let singleGuest = {
        id: guest.id,
        email: guest.email,
        gender: guest.gender,
        title: guest.first_name +" "+guest.last_name,
        start: guest.visited,
        allDay: false,
      };
      return singleGuest;
    },

    showModal() {
       this.isModalVisible = true;
    },

    closeModal() {
       this.isModalVisible = false;
    }
  }
}
</script>


<style lang='scss'>
@import "../styles/variables.scss";
@import "../styles/_queries.scss";
@import "../styles/mixins.scss";

h3 {
  margin: 0;
}

ul {
  margin: 0;
  padding: 0 0 0 1.5em;
}

li {
  margin: 1.5em 0;
  padding: 0;
}

b { /* used for event dates/times */
  margin-right: 3px;
}

.home {
  display: flex;
   @include flexProperties(column, center, center);
  min-height: 100%;
  font-size: 14px;

  &__main {
    flex-grow: 1;
    padding: 3em;

    &-init {
      background-color: rgba(0,170,170,0.1);
      margin-bottom: 70px;
      @include measures(700px, 400px);
      @include borderProperties($secondary, 10px);
      @include  flexProperties(column, center, center);
   

      @include responsive(tablet) {
        width: 500px;
        @include textProperties(25px, $secondary-dark-title);
      }

      @include responsive(mobile) {
        width: 330px;
        @include textProperties(20px, $secondary-dark-title);
      }
    }

    &-init-text {
      @include textProperties(30px, $secondary-dark-title);
      font-weight: bold;

      @include responsive(mobile) {
        @include textProperties(20px, $secondary-dark-title);
      }
    }

    &-init-button {
        cursor: pointer;
        @include textProperties(17px, $white);
        font-weight: 500;
        background-color: $secondary-dark-title;
        @include borderProperties(none, 5px);
        border: none;
        padding: 10px;
        margin-top: 25px;

        @include responsive(mobile) {
          @include textProperties(14px, $white);
        }
    }


  }

  &__header {
    @include textProperties(24px, $secondary-dark-title);
    font-weight: bold;

    @include responsive(mobile) {
      @include textProperties(20px, $secondary-dark-title);
    }
  }

  &__noGuests {
    @include textProperties(20px,$secondary-dark-title);
     font-weight: bold;

    @include responsive(mobile) {
      @include textProperties(17px, $secondary-dark-title);
    }
  }

  &__guest {
      display: flex; 
      flex-direction: column;
      align-items: flex-start;

      &-info { 
        @include flexProperties(row, center, center);
      }

      &-label {
         @include textProperties(15px,$secondary-dark-title);
         font-weight: bold; 
      }

      &-name {
         @include textProperties(15px,$secondary-dark-title);
         margin-left: 10px;
      }
    }
}

.fc { /* the calendar root */
  max-width: 1100px;
  margin: 0 auto;
}


.fc .fc-toolbar-title {
  @include textProperties(22px, $primary);
  @include responsive(tablet) {
    @include textProperties(18px, $primary);
  }

  @include responsive(mobile) {
    @include textProperties(15px, $primary);
  }
}

.fc .fc-button {
  background-color: $primary;
  border: transparent;
  background-color: $primary;
  @include textProperties(16px, $white);

    @include responsive(tablet) {
      @include textProperties(13px, $white);
    }

    @include responsive(mobile) {
      @include textProperties(10px, $white);
    }
}

.fc .fc-button:hover {
  transition: 0.3s;
  background-color: $primary-light;
  
}

.fc .fc-button:active {
  outline: 0;
  box-shadow: none;
  border: none;
}

.fc .fc-button-primary:not(:disabled):active {
  background-color: $primary-light;
}

.fc .fc-button:focus {
  box-shadow: none;
  border: none;
}

.fc .fc-button-primary:active {
  box-shadow: none;
  border: none;
  outline: none;
}


.fc .fc-button-primary:disabled {
  cursor:pointer;
   border-color: transparent;
   background-color: $primary;
   @include textProperties(16px, $white);

    @include responsive(tablet) {
      @include textProperties(13px, $white);
    }

    @include responsive(mobile) {
      @include textProperties(10px, $white);
    }
   
}

.fc .fc-col-header-cell-cushion {
  color: $primary;
  @include textProperties(14px, $primary);
  @include responsive(mobile) {
    @include textProperties(10px, $primary);
  }
}

.fc .fc-daygrid-day-number {
   color: $primary;
   font-weight: 500;
   margin-right: 3px !important;

    @include responsive(mobile) {
      @include textProperties(8px, $primary);
    }
}

.fc .fc-daygrid-more-link {
  @include responsive(tablet) {
    @include textProperties(9px, $primary);
  }

  @include responsive(mobile) {
    @include textProperties(8px, $primary);
  }
}

.fc-daygrid-dot-event:hover {
  background: $primary-light;
}

.fc-direction-ltr .fc-daygrid-event.fc-event-end span {
    @include textProperties(10px, $primary);

  @include responsive(tablet) {
    display: flex;
    flex-direction: column;
    @include textProperties(8px, $primary);
  }

  @include responsive(mobile) {
    display: flex;
    flex-direction: column;
    @include textProperties(6px, $primary);
  }
}

.fc-direction-ltr .fc-daygrid-event.fc-event-end span:hover {
  color: $white;
  transition: 0.3s;
}

.fc-theme-standard .fc-popover-header {
  background-color: $primary-light;
  @include textProperties(12px, $white);
}

.fc .fc-daygrid-event-harness {
  @include textProperties(18px, $primary !important) ;
}

// calndar grid style
.fc-theme-standard td {
  border: 1px solid $primary;
}

.fc .fc-daygrid-day-frame:hover  {
  cursor: pointer;
  background-color: $primary-light-second;
  color: $white;
}

.fc .fc-scrollgrid-liquid {
  border: 2px solid $primary;
  box-shadow: 0 0 10px 0 rgba(143, 12, 84, 0.158);
}

.fc-theme-standard th {
  border: 2px solid $primary;
 
}



// .fc-theme-standard td {
//     width: 150px;
// }

// .fc-col-header-cell {
//     width: 150px;
// }


</style>
