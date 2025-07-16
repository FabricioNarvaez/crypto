<template>
  <div class="contenedor">
    <h1 class="titulo">Cotizador de <span>Criptomonedas</span></h1>
    <div class="contenido">
      <Alert v-if="error">
        {{ error }}
      </Alert>
      <form class="formulario" @submit.prevent="quoteCrypto">
        <div class="campo">
          <label for="moneda">Moneda:</label>
          <select id="moneda" v-model="quote.coin">
            <option value="">-- Seleccione --</option>
            <option v-for="moneda in coins" :key="moneda.codigo" :value="moneda.codigo">
              {{ moneda.texto }}
            </option>
          </select>
          <label for="crypto">Cryptos</label>
          <select id="crypto" v-model="quote.crypto">
            <option value="">-- Seleccione --</option>
            <option v-for="crypto in cryptos" :key="crypto.CoinInfo.Id" :value="crypto.CoinInfo.Name">
              {{ crypto.CoinInfo.FullName }}
            </option>
          </select>
        </div>
        
        <input type="submit" value="Cotizar"/>
      </form>

      <div class="resultContainer">
        <h2>Cotización</h2>

        <div class="result">
          <img :src="'https://cryptocompare.com/' + quotation.IMAGEURL" alt="Imagen crypto">
          <div>
            <p>El precio es de: <span>{{ quotation.PRICE }}</span></p>
            <p>Precio más alto del día: <span>{{ quotation.HIGHDAY }}</span></p>
            <p>Precio más bajo del día: <span>{{ quotation.LOWDAY }}</span></p>
            <p>Variación últimas 24h: <span>{{ quotation.CHANGEPCT24HOUR }} %</span></p>
            <p>Última actualización: <span>{{ quotation.LASTUPDATE }}</span></p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
  import { ref, reactive, onMounted } from 'vue';
  import Alert from './components/Alert.vue';

  const coins = ref([
    { codigo: 'USD', texto: 'Dólar Estadounidense'},
    { codigo: 'MXN', texto: 'Peso Mexicano' },
    { codigo: 'EUR', texto: 'Euro' },
    { codigo: 'GBP', texto: 'Libra Esterlina' }
  ]);

  const error = ref('');
  const cryptos = ref([]);
  const quotation = ref({});
  const quote = reactive({
    coin: '',
    crypto: ''
  });

  const quoteCrypto = () => {
    if (Object.values(quote).includes('')){
      error.value = 'Todos los campos son obligatorios.';
      return;
    };
    error.value = '';
    obtainQuote();
  }

  const obtainQuote = async () => {
    const {coin, crypto} = quote;
    const APIurl = `https://min-api.cryptocompare.com/data/pricemultifull?fsyms=${crypto}&tsyms=${coin}`;
    
    
    const response = await fetch(APIurl);
    const data = await response.json();
    quotation.value = data.DISPLAY[crypto][coin];
  };

  onMounted(() => {
    const APIurl = 'https://min-api.cryptocompare.com/data/top/mktcapfull?limit=20&tsym=USD';
    fetch(APIurl)
      .then(response => response.json())
      .then(({Data}) => cryptos.value = Data)
      .catch(error => console.error('Error al obtener las monedas:', error));
  });
</script>