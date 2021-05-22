<template>
   <div class='demo-app'>
    <div class='demo-app-sidebar'>
      <div class='demo-app-sidebar-section'>
        <h2>All Events ({{guests.length }})</h2>
        <ul>
          <li v-for='guest in guests' :key='guest.id' class="guest_title">
            <b>{{ guest.startStr }}</b>
            <i>{{ guest.title }}</i>
          </li>
        </ul>
      </div>
    </div>
    <div class='demo-app-main'>
      <FullCalendar
        class='demo-app-calendar'
        :options='calendarOptions'
      >
        <template v-slot:eventContent='arg'>
          <b>{{ arg.timeText }}</b>
          <i>{{ arg.event.title }}</i>
        </template>
      </FullCalendar>
    </div>

   <!-- <button
      type="button"
      class="btn"
      @click="showModal"
    >
      Open Modal!
    </button> -->

    <Modal
      v-show="isModalVisible"
      @close="closeModal"
    >
        <template v-slot:header>
            This is a new modal header.
        </template>

        <template v-slot:body>
            This is a new modal body.
        </template>

        <template v-slot:footer>
            This is a new modal footer.
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
        headerToolbar: {
          left: 'prev,next today',
          center: 'title',
          right: 'dayGridMonth,timeGridWeek,timeGridDay'
        },
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
      isModalVisible: false,
    }
  },

  created() {
    this.guestsUrl = guestsUrl;
    this.initCalendar(INITIAL_EVENTS);

    
  }, 

  mounted() {
      
  },

  methods: {


    handleDateSelect(clickInfo) {
        if (clickInfo.event.title) {
          
            console.log("single guest")
        }
        // console.log("SELECT INFO", selectInfo);
        //   let title = prompt('Please enter a new title for your event')
        //   let calendarApi = selectInfo.view.calendar

        //   calendarApi.unselect() // clear date selection

        //   if (title) {
        //     calendarApi.addEvent({
        //       id: createEventId(),
        //       title,
        //       start: selectInfo.startStr,
        //       end: selectInfo.endStr,
        //       allDay: selectInfo.allDay
        //     })
        //   }
    },

    seeGuestInfo(infoGuest) {
           if (infoGuest.event.title) {
               console.log("single guest");
               for (var i in this.calendarOptions.events) {
                if (this.calendarOptions.events[i].title === infoGuest.event.title) {
                    console.log(this.calendarOptions.events[i]);
                    this.showModal();
                }
            }
        }
   
    },

    handleEvents(guests) {
        console.log("GUESTS",guests);
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

      axios.get(`${guestsUrl}`, { params: { offset: true, limit: 15 }, headers: headers }).then(response => {
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

.guest_title {
    display: flex;
    flex-direction: column;
}

h2 {
  margin: 0;
  font-size: 16px;
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

.demo-app {
  display: flex;
  min-height: 100%;
  font-family: Arial, Helvetica Neue, Helvetica, sans-serif;
  font-size: 14px;
}

.demo-app-sidebar {
  width: 300px;
  line-height: 1.5;
  background: #eaf9ff;
  border-right: 1px solid #d3e2e8;
}

.demo-app-sidebar-section {
  padding: 2em;
}

.demo-app-main {
  flex-grow: 1;
  padding: 3em;
}

.fc { /* the calendar root */
  max-width: 1100px;
  margin: 0 auto;
}

</style>
