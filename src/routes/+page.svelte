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

    let mealTime: Date;

    switch (meal) {
      case Meal.Breakfast:
        mealTime = new Date(
          pureDate.getTime() + hoursToMills(Meal.Breakfast + offset)
        );
        break;
      case Meal.Lunch:
        mealTime = new Date(
          pureDate.getTime() + hoursToMills(Meal.Lunch + offset)
        );
        break;
      case Meal.Dinner:
        mealTime = new Date(
          pureDate.getTime() + hoursToMills(Meal.Dinner + offset)
        );
        break;
    }
    return mealTime;
  }

  function getNextMeals(unit: Unit, timeMargin: number): [Meal, Date][] {
    function calculateMeal(meal: Meal) {
      const now = new Date();
      let nextMeal = getMealTime(meal, unit, now);
      if (now.getTime() - hoursToMills(timeMargin) > nextMeal.getTime()) {
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

  console.log(getNextMeals(Unit.Arendus, 0.5));
</script>

<section class="flex flex-col items-center justify-center h-full gap-3">
  <section class="text-lg sm:text-xl text-center mx-6">
    <label for="">On {getYearWeek()}. nädal, ehk </label>
    <select name="" id="" class="border-1 rounded-sm px-1 border-gray-500">
      <option value="">arendusel</option>
      <option value="">kasutajatoel</option>
      <option value="">KIOKe-l</option>
      <option value="">StTaKo-l</option>
      <option value="">StratKomKe-l</option>
    </select>
    <label for="">on õhtusöök:</label>
  </section>
  <h1 class="text-8xl sm:text-9xl font-bold pointer-events-none select-none">
    18:15
  </h1>
</section>

<style lang="scss">
</style>
