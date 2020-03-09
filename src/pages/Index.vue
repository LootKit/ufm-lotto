<template>
	<q-page class="flex flex-center">
		<div class="row q-gutter-md">
			<q-form @submit="onSubmit" @reset="onReset" class="q-gutter-md">
				<q-input filled clearable v-model="steem_account" label="Steem Account *" hint="the steem account paying for this tickets" lazy-rules :rules="[ val => val && val.length > 0 || 'Please type something']" />
				<q-input filled clearable type="number" v-model="ticket_amount" label="# of Tickets *" hint="the amount of tickets you'd like to buy" lazy-rules :rules="[val => val !== null && val !== '' || 'Please type a number', val => val > 0 || 'Please type a proper number']" />
				 <!-- <br><q-slider v-model="ticket_amount" :min="1" :max="1000" :step="1" snap label label-always color="positive" />
				 <span>amount of tickets to buy</span><br> -->

				<q-toggle v-model="gift" label="Make This A Gift Purchase" />
				<q-input v-if="gift" filled clearable v-model="gift_account" label="Gift Account *" hint="the steem account receiving these tickets" lazy-rules :rules="[ val => val && val.length > 0 || 'Please type something']" />

				<p>Pay With:</p>
				<q-btn-toggle v-model="currency" toggle-color="primary" :options="[{label: 'STEEMP', value: 'STEEMP'}, {label: 'UFM', value: 'UFM'}, {label: 'EPC', value: 'EPC'}]" />
				<br><span>(STEEMP = 0.1 | UFM = 4 | EPC = 900)</span>

				<div>
					<q-btn :label="'Buy For '+ticket_cost+' '+currency" type="submit" color="primary" />
					<q-btn label="Reset" type="reset" color="accent" flat class="q-ml-sm" />
				</div>
			</q-form>

			<iframe src="https://titanembeds.com/embed/618206078664179733?defaultchannel=641213768940126218&scrollbartheme=3d-dark" width="400" frameborder="0"></iframe>
		</div>
	</q-page>
</template>

<style scoped lang="stylus">
	p
		font-size 2em
	
	.q-btn-toggle
		margin-top -1em

	span
		margin-top 0.1em
		font-size 0.85em
		font-style italic
</style>

<script>
export default {
	name: 'PageIndex',

	data() {
		return {
			steem_account: '',
			ticket_amount: 1,
			gift: false,
			gift_account: '',
			currency: 'STEEMP',
		}
	},

	computed: {
		ticket_cost() {
			if (this.currency == 'STEEMP') return Number((Number(this.ticket_amount) * 0.1).toFixed(1))
			else if (this.currency == 'UFM') return Number(this.ticket_amount) * 4
			else if (this.currency == 'EPC') return Number(this.ticket_amount) * 900
		}
	},

	mounted () {
		let styles = [
			'background: linear-gradient(#9781FC, #1DC3D6)'
			, 'border: 1px solid #333'
			, 'color: white'
			, 'display: block'
			, 'text-shadow: 0 1px 0 rgba(0, 0, 0, 0.3)'
			, 'box-shadow: 3px 3px 3px rgba(0, 0, 0, 0.4)'
			, 'line-height: 40px'
			, 'text-align: center'
			, 'font-weight: bold'
		].join(';');

		if (window.steem_keychain) {
        	steem_keychain.requestHandshake(() => { console.log('%c Keychain Found! ', styles) })
    	} else console.error('No \'window.steem_keychain\' Found')
	},

	methods: {
		onSubmit() {
			let keychain_title = ''
			if (this.gift) keychain_title = `${this.steem_account} gifting ${this.ticket_amount} lotto tickets to ${this.gift_account} for ${this.ticket_cost} ${this.currency}`
			else {
				if (this.gift_account != '') this.gift_account = ''
				keychain_title = `${this.steem_account} buying ${this.ticket_amount} lotto tickets for ${this.ticket_cost} ${this.currency}`
			}

			let json_data = `{"contractName":"tokens","contractAction":"transfer","contractPayload":{"symbol":"${this.currency}","to":"ufmlotto","quantity":"${this.ticket_cost}","memo":"${this.gift_account}"}}`

			steem_keychain.requestCustomJson(this.steem_account.toLowerCase(), 'ssc-mainnet1', 'Active', json_data, keychain_title, function(response) {
				console.log(response);
			})
		},

		onReset() {
			this.steem_account = null
      		this.ticket_amount = null
			this.gift = false
			this.gift_account = null
		},
	}
}
</script>
