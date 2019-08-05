<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" 
          content="width=device-width, initial-scale=1.0" />
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    
    <title>Mini App</title>
    
    <style>
      body {
      margin: 0;
      padding: 1em;
	  background-color:white;
    }

	div[data-cart-info] span {
		display:inline-block;
		vertical-align:middle;
	}

  span.material-icons{
  font-size:150px;
  }

div [data-credit-card]{
	width:435px;
	min-height:240px;
	border-radius:10px;
	background-color:#5d6874;
}

img[data-card-type]{
	display:block;
	width:120px;
	height:60px;
}

div [data-cc-digits]{
	margin-top:2em;
}

div [data-cc-digits] input{
	color:white;
	font-size:2em;
	line-height:2em;
	border:none;
	background:none;
	margin-right:0.5em;
}

div [data-cc-info]{
	margin-top:1em;
}

div[data-cc-info] input{
	color:white;
	font-size:1.2em;
	border:none;
	background:none;
}

div[data-cc-info] input:nth-child(2){
padding-right:10px;
float:right;
}

button[data-pay-btn]{
	position:fixed;
	width:90%;
	border:1px solid;
	bottom:20px;
}

    [data-cart-info],
    [data-credit-card] {
      transform: scale(0.78);
      margin-left: -3.4em;
    }

    [data-cc-info] input:focus,
    [data-cc-digits] input:focus {
      outline: none;
    }

    .mdc-card__primary-action,
    .mdc-card__primary-action:hover {
      cursor: auto;
      padding: 20px;
      min-height: inherit;
    }

    [data-credit-card] [data-card-type] {
      transition: width 1.5s;
      margin-left: calc(100% - 130px);
    }

    [data-credit-card].is-visa {
      background: linear-gradient(135deg, #622774 0%, #c53364 100%);
    }

    [data-credit-card].is-mastercard {
      background: linear-gradient(135deg, #65799b 0%, #5e2563 100%);
    }

    .is-visa [data-card-type],
    .is-mastercard [data-card-type] {
      width: auto;
    }

    input.is-invalid,
    .is-invalid input {
      text-decoration: line-through;
    }

    ::placeholder {
      color: #fff;
    }
      
    /* Add Your CSS From Here */
    </style>
  </head>
  <body>
    
    <!-- your HTML goes here -->
	<div data-cart-info>
		<h1 class="mdc-typography--headline4"> 
			<span class="material-icons">shopping_cart</span>
			<span data-bill></span>
		</h1>
	</div>
	<div data-credit-card class="mdc-card mdc-card--outlined">
	<div class="mdc-card__primary-action"> 
		<img data-card-type src="https://placehold.it/120x60.png?text=card" alt="">
		<div data-cc-digits>
			<input type="text" size="4" placeholder="----">
			<input type="text" size="4" placeholder="----">
			<input type="text" size="4" placeholder="----">
			<input type="text" size="4" placeholder="----">
		</div>
		<div data-cc-info>
			<input type="text" size="20" placeholder="Name Surname">
			<input type="text" size="6" placeholder="MM/YY">
		</div>
	</div>
	</div>
	<button class="mdc-button" data-pay-btn>Pay Now </button>
	
    
    <script>
      
      const supportedCards = {
        visa, mastercard
      };

      const countries = [
        {
          code: "US",
          currency: "USD",
          currencyName: '',
          country: 'United States'
        },
        {
          code: "NG",
          currency: "NGN",
          currencyName: '',
          country: 'Nigeria'
        },
        {
          code: 'KE',
          currency: 'KES',
          currencyName: '',
          country: 'Kenya'
        },
        {
          code: 'UG',
          currency: 'UGX',
          currencyName: '',
          country: 'Uganda'
        },
        {
          code: 'RW',
          currency: 'RWF',
          currencyName: '',
          country: 'Rwanda'
        },
        {
          code: 'TZ',
          currency: 'TZS',
          currencyName: '',
          country: 'Tanzania'
        },
        {
          code: 'ZA',
          currency: 'ZAR',
          currencyName: '',
          country: 'South Africa'
        },
        {
          code: 'CM',
          currency: 'XAF',
          currencyName: '',
          country: 'Cameroon'
        },
        {
          code: 'GH',
          currency: 'GHS',
          currencyName: '',
          country: 'Ghana'
        }
      ];

      const billHype = () => {
        const billDisplay = document.querySelector('.mdc-typography--headline4');
        if (!billDisplay) return;

        billDisplay.addEventListener('click', () => {
          const billSpan = document.querySelector("[data-bill]");
          if (billSpan &&
            appState.bill &&
            appState.billFormatted &&
            appState.billFormatted === billSpan.textContent) {
            window.speechSynthesis.speak(
              new SpeechSynthesisUtterance(appState.billFormatted)
            );
          }
        });
      };

	  const appState={};

	  const formatAsMoney=(amount, buyerCountry)=>
	  {
		  const country=countries.find(
			  country=>country.country===buyerCountry);
			  if(country){
				  return amount.toLocaleString('en-'+country.code,{style:'currency',currency:country.currency});
			  }else{
				  return amount.toLocaleString('US',{style:'currency',currency:'USD'});
			  }
	  }

	  const flagIfInvalid=(field,isValid)=>{
		  isValid ?
		  field.classList.remove('is-invalid'):field.classList.add('is-invalid');
	  }

	  const expiryDateFormatIsValid=(target)=>{
		  if((target.value.indexOf('/')==2)&&(target.value.length==5)){
			  //care to check if the first 2 and the last 2 chars are numbers?
			  return true;
		  }
		  return false;
	  }

	  const isFuturisticDate=(date)=>{
		  const currDate=newDate();
		  const inputMonth=parseInt(date.value.split('/')[0], 10);
		  if(inputMonth>12|| inputMonth<1){
			  return false
		  }
		  const inputYear=parseInt(date.value.split('/')[1], 10);
		  const twoDigitFullYear=parseInt(currDate.getFullYear().toString().substr(-2), 10);
		  const twoDigitMonth=currDate.getMonth()+1;
		  return (inputYear>twoDigitFullYear)|| (inputYear===twoDigitFullYear&& inputMonth>twoDigitMonth);
	  }
      
	  const detectCardType=({target})=>{
		  const card = document.querySelector('div[data-credit-card]');
		  const cardLogo = document.querySelector('[data-card-type]');
		  if(target.value.startsWith('4')){
			  card.classList.remove('is-mastercard');
			  card.classList.add('is-visa');
			  cardLogo.src=supportedCards.mastercard;
			  return 'is-mastercard';
		  }
	  }


	  const fetchBill = () => {
        const apiHost = 'https://randomapi.com/api';
		const apiKey = '006b08a801d82d0c9824dcfdfdfa3b3c';
		const apiEndpoint = `${apiHost}/${apiKey}`;
        
      };
      
      const startApp = () => {
      };

      startApp();
    </script>
  </body>
</html>
