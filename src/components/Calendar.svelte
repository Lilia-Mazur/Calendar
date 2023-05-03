
<script lang="ts">
  import { onMount } from "svelte";
  import type { Day } from "../models/DayType";
  import type { HistoryGameType } from "../models/HistoryGameType";

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
			let d: string = new Date(prevMonth==11 ? year-1 : year, prevMonth,i+1).toLocaleDateString();
			days.push({name:''+(i+1),enabled:false,date:d,});
		}
		for (let i : number = 0; i<daysInThisMonth; i++) {
			let d: string = new Date(year , month, i+1).toLocaleDateString();
			days.push({name:''+(i+1),enabled:true,date:d,});
		}
		for (let i: number = 0; days.length%7; i++) {
			let d: string = new Date((month == 11 ? year+1 : year),(month+1)%12,i+1).toLocaleDateString();
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
      title: "Игра: DFGDFGGD",
      time: "19:00"
    },
    {
      date: "28/04/2023",
      title: "Игра: DFGDFGGD",
      time: "16:00"
    },
    {
      date: "01/05/2023",
      title: "Игра: DFGDFGGD",
      time: "11:00"
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
        <button on:click={()=>prev()}>&and;</button>
        <button on:click={()=>next()}>&or;</button>
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
  width: max-content;
  margin: auto;
  display: flex;
  background: #1E1D2B;
  border-radius: 16px;
  &__container {
    padding: 14px 24px 17px;
    background: linear-gradient(144.03deg, rgba(78, 77, 96, 0.6) 65%, rgba(174, 120, 225, 0.6) 181%);
    border-radius: 16px;
  }
&__header {
  display: flex;
  justify-content: space-between;
  align-items: center;

  h1 {
    margin: 0;
    font-size: 18px;
    color: #fff;
  }

  button {
    background: transparent;
    border: 1px ;
    padding: 6;
    color: #fff;
    cursor: pointer;
  }
}

  &__content { 
    display: grid;
    width: 100%;
    grid-template-columns: repeat(7, 24px);
    grid-template-rows: 24px;
    grid-auto-rows: 24px;
    gap: 8px;
    overflow: auto;

    .day {
      display: flex;
      align-items: center;
      justify-content: center;
      letter-spacing: 1px;
      font-size: 14px;
      box-sizing: border-box;
      color: #fff;
      position: relative;
      z-index: 1;
      cursor: pointer;
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
      font-size: 12px;
      text-transform: uppercase;
      color: #78778A;;
      text-align: center;
      font-weight: 500;
    }
  }

  &__info {
    margin: 40px 14px;
    margin-left: 16px;
    margin-right: 14px;
    width: 306px;
    display: none;
    &.active {
      display: block;
    }
    p {
      color: #fff;
      font-size: 16px;
    }

    .info__card {
      padding: 8px;
      background: #2DD9E7;
      border-radius: 8px;
    }
  }
}
</style>

