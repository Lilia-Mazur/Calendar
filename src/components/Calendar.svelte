
<script lang="ts">
  import { onMount } from "svelte";
  import type { Day } from "../models/DayType";
  import type { HistoryGameType } from "../models/HistoryGameType";
  import { nextArrow, prevArrow } from "../assets/img";

  const  dayNames = ["пн", "вт", "ср", "чт", "пт", "сб", "вс"];
	const monthNames = ["Январь", "Февраль", "Март", "Апрель", "Май", "Июнь", "Июль", "Август", "Сентябрь", "Октябрь", "Ноябрь", "Декабрь"];


	let now: Date = new Date();
	let year:number = now.getFullYear();	
	let month:number = now.getMonth();
	let days: Day[] = [];

	$: month,year, initMonth();

	function initMonth(): void {
		days = [];

		const firstDay: number = new Date(year, month, 0).getDay();
		const daysInThisMonth: number = new Date(year, month+1, 0).getDate();
    const daysInLastMonth: number = new Date(year, month, 0).getDate();
		const prevMonth: number = month == 0 ? 11 : month-1;

		for (let i: number = daysInLastMonth-firstDay; i<daysInLastMonth; i++) {
			let d: string = new Date(prevMonth==11 ? year-1 : year, prevMonth,i+1).toLocaleDateString("en-GB");
			days.push({name:''+(i+1),enabled:false,date:d,});
		}
		for (let i : number = 0; i<daysInThisMonth; i++) {
			let d: string = new Date(year , month, i+1).toLocaleDateString("en-GB");
			days.push({name:''+(i+1),enabled:true,date:d,});
		}
		for (let i: number = 0; days.length%7; i++) {
			let d: string = new Date((month == 11 ? year+1 : year),(month+1)%12,i+1).toLocaleDateString("en-GB");
			days.push({name:''+(i+1),enabled:false,date:d,});
		}
	}

	function next(): void {
		month++;
		if (month == 12) {
 			year++;
			month=0;
		}
	}
	function prev(): void {
		if (month==0) {
			month=11;
			year--;
		} else {
			month--;
		}
	}

  const historyGames: HistoryGameType[] = [
    {
      date: "29/04/2023",
      title: "DFGDFGGD",
      time: "19:00"
    },
    {
      date: "28/04/2023",
      title: "Игра: DFGDFGGD",
      time: "16:00"
    },
    {
      date: "21/05/2023",
      title: "Игра: DFGDFGGD",
      time: "11:00",
    }
  ]

  let openDayHistory: undefined | string;

  function isGames(day: string): boolean {
    for (let plan of historyGames) {
      if (plan.date === day) {
        return true;
      }
    }
  }

  onMount(() => initMonth)
</script>


<div class="calendar">
  <div class="calendar__container">
    <div class="calendar__header">
      <h1>
        {monthNames[month]} {year}
      </h1>
      <div class="nav">
        <button on:click={()=>prev()}>
          <img src={prevArrow} alt="">
        </button>
        <button on:click={()=>next()}>
          <img src={nextArrow} alt="">
        </button>
      </div>
  
    </div>
    <div class="calendar__content">
      {#each dayNames as name}
      <span class="day-name">{name}</span>
      {/each}
    
      {#each days as day}
          <span 
            class="day"
            class:disabled={!day.enabled}
            class:today={now.toLocaleDateString('en-GB') === day.date}
            class:is-games={isGames(day.date)}
            class:active={openDayHistory === day.date}
            on:click={() => openDayHistory === day.date ? openDayHistory = undefined : openDayHistory = day.date}
          >{day.name}</span>
      {/each}
    </div>
  </div>

  <div class="calendar__info" class:active={openDayHistory}>
    {#each historyGames as game }
      {#if game.date === openDayHistory}
      <p class="info__title">{game.date.split('/').join('.')}</p>
      <div class="info__card">
        <p>{game.title}</p>
        <p>{game.time}</p>
      </div>
      {/if}
    {/each}
  </div>
</div>




<style lang="scss">
.calendar {
  display: flex;
  gap: 50px;
  justify-content: space-around;
  &__container {
    width: max-content;
    background: rgba(29, 29, 43, 0.26);
    border-radius: 18px;
  }
  &__header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 11px 15px 21px;

    h1 {
      margin: 0;
      font-weight: 500;
      font-size: 16px;
      line-height: 20px;
      color: #fff;
    }

    .nav {
      display: flex;
      gap: 16px;
      button {
      border: none;
      background: none;
      cursor: pointer;
      padding: 0;
      img {
        width: 16px;
      }
    }
    }
  }

  &__content { 
    display: grid;
    grid-template-columns: repeat(7, 24px);
    grid-template-rows: 24px;
    grid-auto-rows: 24px;
    gap: 18px 16px;
    padding: 0 20px 21px;
    overflow: auto;

    .day {
      display: flex;
      align-items: center;
      justify-content: center;
      font-weight: 400;
      font-size: 14px;
      line-height: 17px;
      // box-sizing: border-box;
      color: #fff;
      position: relative;
      z-index: 1;
      cursor: pointer;
      &:nth-child(7n-1), &:nth-child(7n){
        color: #D4D7E0;
      }
      &.disabled {
        color: #78778A;
        // cursor: not-allowed;
      }
      &.active {
        background-color: #2DD9E7;
        border-radius: 50%;
      }
      &.is-games {
        position: relative;
        &::after {
          content: "";
          width: 4px;
          height: 4px;
          position: absolute;
          background: linear-gradient(334deg, #CF691E 15%, #FFC85C 86%);
          border-radius: 50%;
          right: 1px;
          top: 3px;
        }
      }
    }
    .today {
      background: #78778A;
      border-radius: 33px;
    }

    .day-name {
      display: flex;
      align-items: center;
      justify-content: center;
      font-weight: 400;
      font-size: 15px;
      line-height: 18px;
      color: #78778A;;
      text-align: center;
    }
  }

  &__info {
    width: 100%;
    max-width: 815px;
    box-sizing: border-box;
    background: rgba(29, 29, 43, 0.26);
    backdrop-filter: blur(60px);
    border-radius: 8px;
    padding: 16px;
    padding-right: 40px;
    display: none;
    gap: 16px;
    flex-direction: column;
    &.active {
      display: flex;
    }
    p {
      color: #fff;
      font-size: 16px;
    }

    .info{
      &__title {
        color: #20C8D5;
        font-weight: 500;
        font-size: 24px;
        line-height: 29px;
        font-variant: small-caps;
      }
      &__card {
        background: rgba(78, 77, 96, 0.6);
        backdrop-filter: blur(60px);
        border-radius: 22px;
        padding: 16px;
        display: flex;
        flex-direction: column;
        gap: 12px;
      }
    }
  }
}
</style>

