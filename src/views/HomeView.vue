<template>
  <main>
    <section class="kredyt">
      <h1>Kalkulator oszczędności na kredycie gotówkowym</h1>
      <div class="input">
        <h3>Początkowa Kwota Kredytu:</h3>
        <input
          v-model="startingLoanAmount"
          type="text"
          placeholder="100 000zł"
          inputmode="numeric"
          @input="
            (event) => (startingLoanAmount = handleInput(event, 200000, true))
          "
        />

        <input
          class="radius"
          v-model="startingLoanAmount"
          type="range"
          id="volume"
          name="volume"
          min="1000"
          max="200000"
          step="100"
        />
      </div>
      <div class="input">
        <h3>Kwota Pozostałego Kredytu:</h3>
        <input
          v-model="remainingLoanAmount"
          type="text"
          placeholder="50 000zł"
          inputmode="numeric"
          @input="
            (event) => (remainingLoanAmount = handleInput(event, 200000, true))
          "
        />

        <input
          class="radius"
          v-model="remainingLoanAmount"
          type="range"
          id="volume"
          name="volume"
          min="1000"
          max="200000"
          step="100"
        />
      </div>

      <div class="input">
        <h3>Początkowa Liczba Rat:</h3>
        <input
          v-model="totalInstallments"
          type="text"
          inputmode="numeric"
          placeholder="60"
          @input="(event) => (totalInstallments = handleInput(event, 120))"
        />

        <input
          class="radius"
          v-model="totalInstallments"
          type="range"
          id="volume"
          name="volume"
          min="1"
          max="120"
          step="1"
        />
      </div>
      <div class="input">
        <h3>Liczba Pozostałych Rat:</h3>
        <input
          v-model="remainingInstallments"
          type="text"
          inputmode="numeric"
          placeholder="30"
          @input="(event) => (remainingInstallments = handleInput(event, 120))"
        />

        <input
          class="radius"
          v-model="remainingInstallments"
          type="range"
          id="volume"
          name="volume"
          min="1"
          max="120"
        />
      </div>
      <div class="input">
        <h3>Rata Dotychczasowego Kredytu:</h3>
        <input
          v-model="currentLoanInstallment"
          type="text"
          inputmode="numeric"
          placeholder="500zł"
          @input="
            (event) => (currentLoanInstallment = handleInput(event, 5000, true))
          "
        />

        <input
          class="radius"
          v-model="currentLoanInstallment"
          type="range"
          id="volume"
          name="volume"
          min="1"
          max="5000"
          step="1"
        />
      </div>
      <div class="input">
        <h3>Prowizja W Procentach:</h3>
        <input
          type="text"
          v-model="commission"
          inputmode="numeric"
          placeholder="2.5%"
          @input="(event) => (commission = handleInput(event, 15, true))"
        />
      </div>

      <button class="check" @click="checkLoan">SPRAWDŹ</button>
    </section>
    <section v-if="result === true" class="wynik">
      <h1>Czy Możesz Zaoszczedzić Na Swoim Kredycie?</h1>
      <div class="wynik">
        <h1 class="result-banner">TAK!!!</h1>
        <h3>Twoja Rata</h3>
        <h2>{{ currentLoanInstallment }}</h2>
        <h3>Nasza Rata</h3>
        <h2>{{ ourInstallment }}</h2>
        <h3>Różnica</h3>
        <h2>{{ difference }}</h2>
        <h3>Łączna Oszczędność</h3>
        <h2>{{ savings }}</h2>

        <h3>Jeśli chcesz dowiedzieć się więcej, zadzwoń:</h3>

        <h2>
          Doradca klienta <br />Patryk Pączek <br />
          +48 785 897 404
        </h2>
      </div>
    </section>
    <section v-if="result === false" class="wynik">
      <h1>Czy możesz zaoszczedzić na swoim kredycie?</h1>
      <h3>
        Wygląda na to, że na chwilę nie jestem w stanie obniżyć twojej raty.
      </h3>
      <h3>Jeśli chcesz dowiedzieć się więcej, zadzwoń:</h3>

      <h2>Doradca klienta Patryk Pączek</h2>
      <h2>+48 785 897 404</h2>
    </section>
  </main>
</template>

<script setup>
import { ref } from "vue";
import Swal from "sweetalert2";

const result = ref(null);
const interest = 10.99;

const startingLoanAmount = ref(null);
const remainingLoanAmount = ref(null);
const totalInstallments = ref(null);
const remainingInstallments = ref(null);
const currentLoanInstallment = ref(null);
const ourInstallment = ref(null);
const difference = ref(null);
const savings = ref(null);
const commission = ref(null);

const checkLoan = () => {
  if (
    remainingInstallments.value === null ||
    remainingLoanAmount.value === null ||
    currentLoanInstallment.value === null
  ) {
    Swal.fire({
      title: "Wypełnij wszystkie pola",
      html: "Pola: <br>-Kwota Pozostałego Kredytu<br> -Liczba Pozostałych Rat <br> -Dotychczasowa Rata <br> muszą zostać uzupełnione",
      icon: "info",
    });
  } else {
    const r = interest / 12 / 100;
    const P = remainingLoanAmount.value;
    const n = remainingInstallments.value;

    ourInstallment.value = (
      P *
      ((r * Math.pow(1 + r, n)) / (Math.pow(1 + r, n) - 1))
    ).toFixed(2);

    if (
      startingLoanAmount.value == null ||
      totalInstallments.value === null ||
      commission.value === null
    ) {
      difference.value = (
        Number(currentLoanInstallment.value) - Number(ourInstallment.value)
      ).toFixed(2);
    } else {
      const computedComisson = Number(
        startingLoanAmount.value * (commission.value / 100)
      );
      const monthlyComisson = Number(
        computedComisson / totalInstallments.value
      );

      difference.value = (
        Number(currentLoanInstallment.value) +
        Number(monthlyComisson) -
        Number(ourInstallment.value)
      ).toFixed(2);
    }

    savings.value = (difference.value * n).toFixed(2);

    if (savings.value > 0) {
      result.value = true;
    } else {
      result.value = false;
    }
  }
};

const handleInput = (event, max, decimalPoint = false) => {
  let value = event.target.value;

  if (Number(value) <= max) {
    if (decimalPoint) {
      return value
        .replace(/[^\d.,]/g, "")
        .replace(",", ".")
        .replace(/^0+(\d)/, "$1")
        .replace(/(\.\d{2})\d+/, "$1")
        .replace(/(\..*)\./g, "$1");
    } else {
      return value.replace(/\D/g, "").replace(/^0+(\d)/, "$1");
    }
  }
  return max;
};
</script>

<style scoped>
main {
  background-color: #f5f5f5;
  border-radius: 10px;
  /* user-select: none; */
}

h2,
h3 {
  color: #771747;
}

h1 {
  text-align: center;
  margin-bottom: 25px;
  font-weight: bold;
  background: linear-gradient(135deg, #771747, #ff0008);
  -webkit-background-clip: text; /* Wsparcie dla WebKit (Chrome, Safari) */
  background-clip: text; /* Standardowe */
  color: transparent;
}

input {
  border: solid #ddd 0.1px;
  border-radius: 7px;
  padding-left: 10px;
  padding-right: 10px;
  background-color: transparent;
  font-size: 17px;
  height: 45px;
  color: #771747;
}

.input {
  display: flex;
  flex-direction: column;
  gap: 5px;
  position: relative;
}

.radius {
  cursor: pointer;
}

.kredyt {
  display: flex;
  flex-direction: column;
  padding: 30px;
  gap: 20px;
}

.check {
  height: 50px;
  border-radius: 15px;
  border: none;
  font-size: 20px;
  color: white;
  font-weight: bold;
  background: linear-gradient(135deg, #771747, #b81d23);
  cursor: pointer;
}

.wynik {
  background-color: #e1e1e1;
  border-radius: 10px;
  display: flex;
  flex-direction: column;
  gap: 15px;
  /* text-align: center; */
  padding: 30px;
  text-align: center;
}

.wynik h2 {
  text-align: center;
}

.result-banner {
  height: 50px;
  background: linear-gradient(135deg, #771747, #b81d23);
  color: white;
  border-radius: 10px;
  flex-direction: column;
  align-content: space-around;
}
.radius {
  accent-color: #ad1f66;
  border: none;
}
</style>
