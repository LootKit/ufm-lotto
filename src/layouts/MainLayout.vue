<template>
	<q-layout view="hHh lpr lff">
		<q-header elevated>
			<q-toolbar>
				<q-btn flat dense round icon="menu" aria-label="Menu" @click="leftDrawerOpen = !leftDrawerOpen" />

				<q-avatar>
					<img src="~assets/logo.png" />
					<q-badge floating color="accent">beta</q-badge>
				</q-avatar>

				<q-toolbar-title>
					UpFundMe LOTTO
				</q-toolbar-title>

				<div>v0.01</div>
			</q-toolbar>
		</q-header>

		<q-drawer v-model="leftDrawerOpen" show-if-above bordered content-class="bg-grey-1">
			<q-list>
				<q-item-label header class="text-grey-8">
					Essential Links
				</q-item-label>
				<EssentialLink v-for="link in essentialLinks" :key="link.title" v-bind="link" />
			</q-list>
		</q-drawer>

		<q-page-container>
			<router-view />
		</q-page-container>
	</q-layout>
</template>

<script>
import EssentialLink from 'components/EssentialLink'

export default {
	name: 'MainLayout',

	components: {
		EssentialLink
	},

	data () {
		return {
			leftDrawerOpen: false,
			essentialLinks: [
				{
					title: 'Homepage',
					caption: 'www.upfund.me',
					icon: 'home',
					link: 'https://www.upfund.me/'
				},
				{
					title: 'Steem Account',
					caption: '@upfundme',
					icon: 'account_circle',
					link: 'https://www.steempeak.com/@upfundme'
				},
				{
					title: 'Discord Channel',
					caption: 'chat.quasar.dev',
					icon: 'chat',
					link: 'https://discord.gg/JqnXeqG'
				},
			]
		}
	}
}
</script>
