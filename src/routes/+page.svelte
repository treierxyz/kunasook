<script lang="ts">
  enum Unit {
    Kasutajatugi = 0,
    Arendus = 1,
    StratKomKe = 2,
    KIOKe = 3,
    StTaKo = 3,
  }

  enum Meal {
    Breakfast = 7,
    Lunch = 12 + 35 / 60,
    Dinner = 18,
  }

  const hoursToMills = (x: number) => x * 3600 * 1000;

  function getUnitOrderFromWeek(week: number, unit: Unit) {
    return ((-week % 4) + 4 + unit) % 4;
  }

  function getYearWeek(date: Date = new Date()) {
    date.setHours(0, 0, 0, 0);
    date.setDate(date.getDate() + 3 - ((date.getDay() + 6) % 7));
    const week1 = new Date(date.getFullYear(), 0, 4);
    return (
      1 +
      Math.round(
        ((date.getTime() - week1.getTime()) / 86400000 -
          3 +
          ((week1.getDay() + 6) % 7)) /
          7
      )
    );
  }

  function getOrdinalDay(date: Date) {
    const startDate = new Date("2025-09-08T00:00:00");
    return Math.floor(
      (date.getTime() - startDate.getTime()) / (1000 * 3600 * 24)
    );
  }

  function getMealTime(meal: Meal, unit: Unit, date: Date): Date {
    const pureDate = structuredClone(date);
    pureDate.setHours(0, 0, 0, 0);
    const ordinalDay = getOrdinalDay(date);
    const ordinalWeek = Math.floor(ordinalDay / 7);
    const offset = getUnitOrderFromWeek(ordinalWeek, unit) * (10 / 60);
    const weekend = [6, 0].includes(date.getDay());

    let mealTime: Date;

    mealTime = new Date(
      pureDate.getTime() +
        hoursToMills(
          meal + offset + (weekend && meal === Meal.Breakfast ? 1 : 0)
        )
    );

    switch (meal) {
      case Meal.Breakfast:
        if (unit === Unit.Arendus && !weekend) {
          mealTime = new Date(pureDate.getTime() + hoursToMills(6 + 50 / 60));
        }
        break;
    }
    return mealTime;
  }

  function getNextMeals(unit: Unit, timeMargin: number): [Meal, Date][] {
    function calculateMeal(meal: Meal) {
      const now = new Date();
      let nextMeal = getMealTime(meal, unit, now);
      if (now.getTime() - hoursToMills(timeMargin) >= nextMeal.getTime()) {
        now.setDate(now.getDate() + 1);
        nextMeal = getMealTime(meal, unit, now);
      }
      return nextMeal;
    }

    const result: [Meal, Date][] = [
      [Meal.Breakfast, calculateMeal(Meal.Breakfast)],
      [Meal.Lunch, calculateMeal(Meal.Lunch)],
      [Meal.Dinner, calculateMeal(Meal.Dinner)],
    ];
    result.sort((a, b) => (a[1] > b[1] ? 1 : -1));
    return result;
  }

  function translateMeals(meal: Meal) {
    switch (meal) {
      case Meal.Breakfast:
        return "hommikusöök";
      case Meal.Lunch:
        return "lõuna";
      case Meal.Dinner:
        return "õhtusöök";
      default:
        break;
    }
  }

  // biome-ignore lint/style/useConst: svelte state is not const
  let selectedUnit: Unit = $state(Unit.Arendus);
</script>

<section class="flex flex-col items-center justify-center h-full gap-3">
  <section class="text-lg sm:text-xl text-center mx-6">
    <label for="">On {getYearWeek()}. nädal, ehk </label>
    <select
      name=""
      id=""
      bind:value={selectedUnit}
      class="border-1 rounded-sm px-1 border-gray-500"
    >
      <option value={Unit.Arendus}>arendusel</option>
      <option value={Unit.Kasutajatugi}>kasutajatoel</option>
      <option value={Unit.KIOKe}>KIOKe-l</option>
      <option value={Unit.StTaKo}>StTaKo-l</option>
      <option value={Unit.StratKomKe}>StratKomKe-l</option>
    </select>
    <label for=""
      >on {translateMeals(getNextMeals(selectedUnit, 0.5)[0][0])}:</label
    >
  </section>
  <h1 class="text-8xl sm:text-9xl font-bold pointer-events-none select-none">
    {getNextMeals(selectedUnit, 0.5)[0][1].toLocaleTimeString("et-EE", {
      hour: "2-digit",
      minute: "2-digit",
    })}
  </h1>
  <p class="text-lg text-center mx-6">
    Lisaks, {translateMeals(getNextMeals(selectedUnit, 0.5)[1][0])} on kell
    <b
      >{getNextMeals(selectedUnit, 0.5)[1][1].toLocaleTimeString("et-EE", {
        hour: "2-digit",
        minute: "2-digit",
      })}</b
    >
    ja {translateMeals(getNextMeals(selectedUnit, 0.5)[2][0])} on kell
    <b>
      {getNextMeals(selectedUnit, 0.5)[2][1].toLocaleTimeString("et-EE", {
        hour: "2-digit",
        minute: "2-digit",
      })}
    </b>.
  </p>
</section>

<style lang="scss">
</style>
