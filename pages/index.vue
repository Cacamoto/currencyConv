<script setup>
const flipped = ref(false);
let amount = ref(1);

let defaultCurrency = ref({
  name: "EUR",
  symbol: "€",
  icon: "circle-flags:european-union",
});
let currenciesToFetch = ref("USD,GBP,JPY,NOK,PLN,RUB");

const handleCurrencySelection = async (currency) => {
  defaultCurrency.value = {
    name: currency.name,
    symbol: currency.symbol,
    icon: currency.icon,
  };
  getCurrencies();
  flipped.value = false;
};

const getIcon = (currencyName) => {
  const currency = currencies.find(
    (currency) => currency.name === currencyName
  );
  return currency.icon;
};

const getSymbol = (currencyName) => {
  const currency = currencies.find(
    (currency) => currency.name === currencyName
  );
  return currency.symbol;
};

const { data } = useFetch(
  `https://api.freecurrencyapi.com/v1/latest?apikey=O003a5MKqnSHs13meWyJasA4ps5ThDSP84VDoxhz&`,
  {
    params: {
      currencies: computed(() => currenciesToFetch.value),
      base_currency: computed(() => defaultCurrency.value.name),
    },
    key: defaultCurrency.value.name,
  }
);

const currencies = [
  {
    name: "EUR",
    symbol: "€",
    icon: "circle-flags:european-union",
  },
  { name: "USD", symbol: "$", icon: "circle-flags:us" },
  {
    name: "GBP",
    symbol: "£",
    icon: "circle-flags:uk",
  },
  {
    name: "JPY",
    symbol: "¥",
    icon: "circle-flags:jp",
  },
  {
    name: "NOK",
    symbol: "kr",
    icon: "circle-flags:no",
  },
  {
    name: "PLN",
    symbol: "zł",
    icon: "circle-flags:pl",
  },
  {
    name: "RUB",
    symbol: "₽",
    icon: "circle-flags:ru",
  },
];

const getCurrencies = () => {
  const currencyNames = currencies
    .filter((currency) => currency.name !== defaultCurrency.value.name)
    .map((currency) => currency.name)
    .join(",");
  currenciesToFetch.value = currencyNames;
};
getCurrencies();
</script>

<template>
  <div
    class="text-xl font-bold uppercase bg-gradient-to-r from-gray-100 via-gray-300 to-gray-200 text-transparent bg-clip-text z-10 mb-4"
  >
    <span class="text-3xl">V</span>aliutų <span class="text-3xl">K</span>ursai
  </div>
  <Transition name="card-flip">
    <div class="card" :class="{ flipped: flipped }">
      <!-- Main view -->
      <div
        class="card-front relative flex flex-col items-center justify-start w-[90vw] max-w-[24rem] bg-neutral-900/75 rounded z-10 shadow-md backdrop-blur-md text-neutral-100 overflow-hidden"
      >
        <div
          class="flex flex-row flex-wrap items-center justify-center gap-6 w-[calc(100%-2rem)] bg-black/50 mt-[1rem] p-2 rounded"
        >
          <div class="flex flex-col">
            <input
              type="number"
              class="w-[8rem] h-[2rem] bg-transparent border rounded border-neutral-600 p-2 text-neutral-100 focus:outline-none focus:border-neutral-400"
              v-model="amount"
            />
          </div>

          <div
            class="flex items-center justify-center gap-2 text-xl cursor-pointer scale-100 hover:scale-105 transition-all duration-300 ease-in-out"
            @click="flipped = true"
          >
            <Icon :name="defaultCurrency.icon" class="w-8 h-8" />
            <span>{{ defaultCurrency.name }}</span>
            <span>{{ defaultCurrency.symbol }}</span>
          </div>
        </div>
        <!-- Retrieved Data -->
        <div
          class="flex flex-1 flex-col w-[calc(100%-2rem)] h-[calc(100%-2rem)] mt-[1rem] mb-[1rem] gap-4"
        >
          <TransitionGroup
            tag="ul"
            class="relative flex flex-col gap-4"
            name="dropIn"
            appear
            mode="out-in"
          >
            <li
              v-for="(answer, key) in data.data"
              class="relative flex flex-row items-center justify-start gap-4 w-full bg-black/50 p-3 rounded cursor-pointer scale-100 hover:scale-105 transition-all duration-300 ease-in-out z-[5]"
              :key="key"
              @click="
                handleCurrencySelection({
                  name: key,
                  symbol: getSymbol(key),
                  icon: getIcon(key),
                })
              "
            >
              <span class="w-8">{{ key }}</span>
              <span class="font-bold text-xl w-6">{{ getSymbol(key) }}</span>
              <Icon :name="getIcon(key)" class="w-8 h-8" />
              <span>{{ (answer * amount).toFixed(6) }}</span>
            </li>
          </TransitionGroup>
        </div>
      </div>
      <!-- Currency selector -->
      <div
        class="card-back relative flex flex-col items-center justify-start w-[90vw] max-w-[24rem] pb-[1rem] bg-neutral-900 rounded z-10 shadow-md backdrop-blur-md text-neutral-100"
      >
        <div
          class="flex flex-row flex-wrap items-center justify-start gap-2 w-[calc(100%-2rem)] bg-black/50 mt-[1rem] p-2 rounded cursor-pointer scale-100 hover:scale-105 transition-all duration-300 ease-in-out"
          v-for="currency in currencies"
          :key="currency.name"
          @click="handleCurrencySelection(currency)"
        >
          <Icon :name="currency.icon" class="w-8 h-8" />
          <span>{{ currency.name }}</span>
          <span>{{ currency.symbol }}</span>
        </div>
      </div>
    </div>
  </Transition>
</template>

<style scoped>
.card {
  position: relative;
  width: 95%;
  height: 100%;
  max-width: 400px;
  max-height: 510px;
  transition: transform 0.6s;
  transform-style: preserve-3d;
}

.card-front,
.card-back {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
}

.card-front {
  z-index: 2;
}

.card-back {
  transform: rotateY(180deg);
}

.card.flipped {
  transform: rotateY(180deg);
}

.card-flip-enter-active,
.card-flip-leave-active {
  transition: all 0.6s ease;
}

.card-flip-enter,
.card-flip-leave-to {
  transform: rotateY(180deg);
}
.dropIn-enter-active {
  animation: categoryDropIn 0.4s ease;
}
.dropIn-leave-active {
  animation: categoryDropOut 0.4s ease;
  position: absolute;
  z-index: 1;
}
.dropIn-move {
  transition: transform 0.4s ease;
}
@keyframes categoryDropIn {
  0% {
    opacity: 0;
    scale: 0.3;
    transform: translateX(-100%);
  }
  100% {
    opacity: 1;
    scale: 1;
    transform: translateX(0);
  }
}
@keyframes categoryDropOut {
  0% {
    opacity: 1;
    scale: 1;
  }
  100% {
    opacity: 0;
    scale: 0;
  }
}
</style>
