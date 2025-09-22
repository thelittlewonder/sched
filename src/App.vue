<script>
import DateUnit from './components/DateUnit.vue'
import html2canvas from 'html2canvas';
import holidays from './assets/holidays.json'
import confetti from "canvas-confetti";

export default {
  name: 'App',
  components: {
    DateUnit
  },
  data() {
    return {
      currentDate: new Date(),
      userCountry: '',
      holidaysEnabled: true
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
    holidaySet() {
      const list = holidays[this.userCountry] || [];
      return new Set(list);
    },
    daysInMonth() {
      const startOfMonth = new Date(this.currentDate.getFullYear(), this.currentDate.getMonth(), 1);
      const endOfMonth = new Date(this.currentDate.getFullYear(), this.currentDate.getMonth() + 1, 0);
      const days = [];
      const startDay = (startOfMonth.getDay() === 0) ? 6 : startOfMonth.getDay() - 1; // Adjust for Monday start
      for (let i = 0; i < startDay; i++) {
        days.push({ day: '', date: null, isWeekend: null, isPublicHoliday: null });
      }

      for (let i = 1; i <= endOfMonth.getDate(); i++) {
        const date = new Date(this.currentDate.getFullYear(), this.currentDate.getMonth(), i)
        const isWeekend = date.getDay() === 0 || date.getDay() === 6;
        const yyyyMmDd = this.toYMDLocal(date)
        const isPublicHoliday = this.holidaysEnabled && this.holidaySet.has(yyyyMmDd);
        days.push({ day: i, date, isWeekend, isPublicHoliday, country: this.userCountry });
      }

      while (days.length % 7 !== 0) {
        days.push({ day: '', date: null, isWeekend: null, isPublicHoliday: null });
      }

      return days;
    }
  },
  mounted() {
    const lang = navigator.language || navigator.userLanguage || 'en-GB';
    if (lang.startsWith('en-GB')) this.userCountry = 'UK';
    else if (lang.startsWith('en-US')) this.userCountry = 'USA';
    else if (lang.startsWith('en-IE')) this.userCountry = 'IE';
    else if (lang.startsWith('fr')) this.userCountry = 'FR';
    else if (lang.startsWith('de')) this.userCountry = 'DE';
    else if (lang.startsWith('en-SG') || lang.startsWith('ms-SG')) this.userCountry = 'SG';
    else this.userCountry = 'UK'; // fallback

    window.addEventListener("keydown", this.handleKeydown);
  },
  beforeUnmount() {
    window.removeEventListener("keydown", this.handleKeydown);
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
    handleKeydown(e) {
      if (e.key === "ArrowLeft") {
        this.prevMonth();
      } else if (e.key === "ArrowRight") {
        this.nextMonth();
      }
    },
    isToday(date) {
      if (!date) return false;
      const today = new Date();
      return today.toDateString() === date.toDateString();
    },
    toYMDLocal(d) {
      const y = d.getFullYear();
      const m = String(d.getMonth() + 1).padStart(2, '0');
      const day = String(d.getDate()).padStart(2, '0');
      return `${y}-${m}-${day}`;
    },
    downloadAsJpg() {
      const captureElement = document.getElementById('capture');
      html2canvas(captureElement).then(canvas => {
        const link = document.createElement('a');
        link.href = canvas.toDataURL('image/jpeg');
        link.download = this.formattedMonthYear + '.jpg';
        link.click();
        confetti({
          particleCount: 120,
          spread: 120,
          origin: { y: 0.8, x: 0.5 }
        });
      });
    }
  }
}

</script>

<template>
  <aside>
    <p>This is a big screen activity dawg. <a href='https://threads.net/abh_.shek' class="threads">@ me</a> if you want.</p>
  </aside>
  <main>
    <div class="calendar-wrapper" id="capture">
      <div class="actionbar">
        <div class="dateSlider">
          <img src="./assets/icons/back.svg" alt="previous" @click="prevMonth" />
          <span>{{ formattedMonthYear }}</span>
          <img src="./assets/icons/forward.svg" alt="forward" @click="nextMonth" />
        </div>
        <label class="switch">
          <input type="checkbox" v-model="holidaysEnabled" />
          <span class="slider"></span>
          <span class="label-text">Local holidays ({{ userCountry }})</span>
        </label>
      </div>
      <div class="calendar">
        <div class="day-header">
          <div class="day-name" v-for="day in dayNames" :key="day">{{ day }}</div>
        </div>
        <div class="grid">
          <DateUnit v-for="day, index in daysInMonth" :calDate=day :key="day.date"
            :is-locked="day.isWeekend || day.isPublicHoliday" />
        </div>
      </div>
    </div>

    <div class="sidebar">

      <button @click="downloadAsJpg" class="download-btn">
        <img src="./assets/icons/download.svg" alt="download image" title="Download as jpg" />
        <label>Download</label>
      </button>

      <div class="details">
        <p class="memo"><span>sched</span> is a tiny utility to visualise your cal & share it with your colleagues,
          friends, and enemies.</p>
        <a href='https://threads.net/abh_.shek' class="threads"><img src="./assets/icons/threads.svg"
            alt="threads" />built by
          abh_.shek</a>
      </div>

    </div>



  </main>
</template>

<style scoped lang="scss">
@media (max-width: 1023px) {
  main {
    display: none !important;
  }
}

@media (min-width: 1024px) and (max-width: 1299px) {
  aside {
    display: none !important;
  }

  main {
    display: flex;
    flex-direction: column;

    .sidebar {
      padding: 24px;
    }
  }
}

@media (min-width: 1300px) {
  aside {
    display: none !important;
  }

  main {
    display: flex;
    flex-direction: row;
    align-items: stretch;

    .sidebar {
      padding: 40px;
      max-width: 300px;
    }
  }
}

aside {
  color: var(--default-base-color);
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  font-size: 14px;
  line-height: 24px;
  padding: 20px;

  p {
    text-align: center;

    a {
      opacity: 0.5;
      color: var(--default-base-color);
      text-decoration: none;

      &:hover {
        opacity: 1;
      }
    }
  }
}

main {
  display: flex;
  max-width: 1800px;

  .sidebar {
    background: #F7F9F9;
    display: flex;
    flex-direction: column;
    justify-content: space-between;

    .download-btn {
      display: flex;
      flex-direction: row;
      align-items: center;
      justify-content: center;
      font-family: inherit;
      gap: 0.75rem;
      background-color: white;
      border: 1px solid var(--default-base-color);
      border-radius: 8px;
      padding: 8px 16px;

      label {
        cursor: pointer;
      }

      &:hover {
        cursor: pointer;
        scale: 0.99;
        background-color: #F7F9F9;
        transition: background-color 0.2s ease-in-out;
      }
    }

    .details {
      color: var(--default-base-color);
      display: flex;
      flex-direction: column;
      row-gap: 8px;

      .memo {
        font-size: 12px;
        line-height: 20px;

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

  .calendar-wrapper {
    display: flex;
    flex-direction: column;
    padding: 24px;

    .actionbar {
      display: flex;
      flex-direction: row;
      justify-content: space-between;
      margin-bottom: 20px;

      .switch {
        opacity: 0;
        transition: opacity 0.3s ease-in-out;
        --h: 18px;
        position: relative;
        display: inline-flex;
        align-items: center;
        gap: 1rem;
        cursor: pointer;
        user-select: none;
        font-size: 14px;
        color: var(--default-base-color);

        input {
          position: absolute;
          inset: 0 0 auto auto;
          width: 40px;
          height: var(--h);
          opacity: 0;
          cursor: inherit;
        }

        .slider {
          position: relative;
          width: 40px;
          height: var(--h);
          background-color: rgba(23, 32, 42, 0.2);
          border-radius: var(--h);
          transition: background-color 0.2s;
          flex-shrink: 0;

          &::before {
            content: "";
            position: absolute;
            height: 12px;
            width: 12px;
            left: 5px;
            bottom: 3px;
            background-color: #fff;
            border-radius: 50%;
            box-shadow: 0 1px 3px rgba(0, 0, 0, 0.2);
            transition: transform 0.2s;
          }
        }

        input:checked+.slider {
          background-color: var(--default-base-color);

          &::before {
            transform: translateX(18px);
          }
        }

        .label-text {
          display: inline-flex;
          align-items: center;
          height: var(--h);
          line-height: 1;
          color: inherit;
          opacity: 0.55;
          transition: opacity 0.2s;
        }

        input:checked+.slider+.label-text {
          opacity: 1;
        }
      }


      .dateSlider {
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
          cursor: pointer;
          margin: 0;
          padding: 0 8px 0 0;
        }
      }

    }


    &:hover .switch {
      opacity: 1;
      pointer-events: auto;
    }

    .calendar {
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
}
</style>
