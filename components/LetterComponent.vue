<template>
	<div 
	class="flex flex-col items-center justify-center aspect-square rounded w-16 h-16"
	:class="{
		'bg-green-500': statut == 'ACCEPTED', 
		'bg-red-500': statut == 'REJECTED', 
		'bg-yellow-500': statut == 'FOUND', 
		'bg-[#0e141b]': statut == 'NEUTRAL', 
		'bg-cyan-600': statut == 'ACTIVED'
	}">
		<p class="text-white font-bold text-4xl uppercase">{{ letter }}</p>
	</div>
</template>

<script>
	export default {
		name: 'LetterComponent',
		props: {
			letter: {
				type: String,
				default: '.'
			},
			statut: {
				type: String,
				default: 'NEUTRAL'
			}
		}
	}
</script>