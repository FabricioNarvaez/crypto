<template>
  <div class="contenedor">
    <h1 class="titulo">Cotizador de <span>Criptomonedas</span></h1>
    <div class="contenido">
      <form class="formulario">
        <div class="campo">
          <label for="moneda">Moneda:</label>
          <select id="moneda">
            <option value="">-- Seleccione --</option>
            <option v-for="moneda in monedas" :key="moneda.codigo" :value="moneda.codigo">
              {{ moneda.texto }}
            </option>
          </select>
          <label for="crypto">Cryptos</label>
          <select id="crypto">
            <option value="">-- Seleccione --</option>
            <option v-for="crypto in cryptos" :key="crypto.CoinInfo.Id" :value="crypto.CoinInfo.Name">
              {{ crypto.CoinInfo.FullName }}
            </option>
          </select>
        </div>
        
        <input type="submit" value="Cotizar"/>
      </form>
    </div>
  </div>
</template>

<script setup>
  import { ref, onMounted } from 'vue';

  const monedas = ref([
    { codigo: 'USD', texto: 'Dólar Estadounidense'},
    { codigo: 'MXN', texto: 'Peso Mexicano' },
    { codigo: 'EUR', texto: 'Euro' },
    { codigo: 'GBP', texto: 'Libra Esterlina' }
  ]);

  const cryptos = ref([]);

  onMounted(() => {
    const APIurl = 'https://min-api.cryptocompare.com/data/top/mktcapfull?limit=20&tsym=USD';
    fetch(APIurl)
      .then(response => response.json())
      .then(({Data}) => cryptos.value = Data)
      .catch(error => console.error('Error al obtener las monedas:', error));
  });
</script>