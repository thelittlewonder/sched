<script>
import DateUnit from './components/DateUnit.vue'
import html2canvas from 'html2canvas';
export default {
  name: 'App',
  components: {
    DateUnit
  },
  data() {
    return {
      currentDate: new Date() // Initialize with current date
    };
  },
  computed: {
    dayNames() {
      return ['Mo', 'Tu', 'We', 'Th', 'Fr', 'Sa', 'Su'];
    },
    formattedMonthYear() {
      const options = { year: 'numeric', month: 'short' };
      return this.currentDate.toLocaleDateString(undefined, options);
    },
    daysInMonth() {
      const startOfMonth = new Date(this.currentDate.getFullYear(), this.currentDate.getMonth(), 1);
      const endOfMonth = new Date(this.currentDate.getFullYear(), this.currentDate.getMonth() + 1, 0);
      const days = [];

      // Fill in the days before the start of the month
      const startDay = (startOfMonth.getDay() === 0) ? 6 : startOfMonth.getDay() - 1; // Adjust for Monday start
      for (let i = 0; i < startDay; i++) {
        days.push({ day: '', date: null });
      }

      // Fill in the days of the month
      for (let i = 1; i <= endOfMonth.getDate(); i++) {
        days.push({ day: i, date: new Date(this.currentDate.getFullYear(), this.currentDate.getMonth(), i) });
      }

      // Fill in the days after the end of the month
      while (days.length < 35) { // Assuming 5 rows
        days.push({ day: '', date: null });
      }

      return days;
    }
  },
  methods: {
    prevMonth() {
      const currentMonth = this.currentDate.getMonth();
      const currentYear = this.currentDate.getFullYear();
      this.currentDate = new Date(currentYear, currentMonth - 1, 1);
    },
    nextMonth() {
      const currentMonth = this.currentDate.getMonth();
      const currentYear = this.currentDate.getFullYear();
      this.currentDate = new Date(currentYear, currentMonth + 1, 1);
    },
    isToday(date) {
      if (!date) return false; // Handle null dates
      const today = new Date();
      return today.toDateString() === date.toDateString();
    },
    downloadAsJpg() {
      const captureElement = document.getElementById('capture');
      
      // Use html2canvas to capture the div as a canvas
      html2canvas(captureElement).then(canvas => {
        // Convert the canvas to a data URL in JPG format
        const imgData = canvas.toDataURL('image/jpeg');

        // Create a temporary link element
        const link = document.createElement('a');
        link.href = imgData;
        link.download = this.formattedMonthYear + '.jpg'; // Set the desired file name

        // Trigger the download
        link.click();
      });
    }
  }
}

</script>

<template>
  <main>
    <div class="sidebar">

      <div class="actionbar">
        <div class="slider">
          <img src="./assets/icons/back.svg" alt="previous" @click="prevMonth" />
          <span>{{ formattedMonthYear }}</span>
          <img src="./assets/icons/forward.svg" alt="forward" @click="nextMonth" />
        </div>
        <img src="./assets/icons/download.svg" alt="download image" class="download-btn" title="Download as jpg" @click="downloadAsJpg"/>
      </div>

      <div class="details">
        <p class="divider">
          –
        </p>
        <p class="memo"><span>sched</span> is a tiny utility to visualise your cal & share it with your colleagues,
          friends, and enemies.</p>
        <p class="divider">
          –
        </p>
        <a href='https://threads.net/abh_.shek' class="threads"><img src="./assets/icons/threads.svg" />built by
          abh_.shek</a>
      </div>

    </div>

    <div class="calendar" id="capture">
      <div class="day-header">
        <div class="day-name" v-for="day in dayNames" :key="day">{{ day }}</div>
      </div>
      <div class="grid">
        <DateUnit :calDate=day v-for="day in daysInMonth" :key="day.date" />
      </div>
    </div>

  </main>
</template>

<style scoped lang="scss">
main {
  display: flex;
  flex-direction: row;
  //max-width: 1440px;
  .actionbar{
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    .download-btn{
      cursor: pointer;
    }
  }

  .sidebar {
    background: #F7F9F9;
    padding: 40px;

    .divider {
      margin: 24px 0;
    }

    .slider {
      display: flex;
      align-items: center;
      margin-bottom: 10px;
      font-weight: 700;
      font-size: 14px;
      text-transform: uppercase;
      letter-spacing: 1px;
      column-gap: 12px;

      img {
        border: none;
        background-color: #F7F9F9;
        cursor: pointer;
        margin: 0;
        padding: 0;
      }
    }

    .details {
      color: var(--default-base-color);
      display: flex;
      flex-direction: column;
      row-gap: 8px;

      .memo {
        font-size: 14px;
        line-height: 24px;

        span {
          font-weight: 700;
        }
      }

      .plug {
        font-size: 12px;
        line-height: 24px;
        opacity: 0.5;
      }

      .threads {
        display: flex;
        align-items: center;
        margin-top: 24px;
        font-size: 12px;
        opacity: 0.5;
        transition: all 0.3s ease;
        color: var(--default-base-color);
        text-decoration: dotted;

        img {
          padding: 4px 0;
          margin-right: 8px;
          height: 20px;
        }

        &:hover {
          opacity: 1;
        }
      }
    }
  }


  .calendar {
    padding: 24px;

    .day-header {
      display: grid;
      grid-template-columns: repeat(7, 1fr);
      text-align: center;
      margin-bottom: 4px;
      font-size: 12px;
      color: var(--default-base-color);
    }

    .day-name {
      padding: 20px 0;
      background: #F7F9F9;
    }

    .grid {
      display: grid;
      grid-template-columns: repeat(7, 1fr);
      gap: 4px;
    }
  }

}
</style>
