<template>
	<div>
		<!--<div class="hide">
			<mcalendar :child="date0"></mcalendar>
			<mcalendar :child="date1"></mcalendar>
		</div>-->
		<div>
			<mdatapick :child='mdatapickdata' @cllback="callback"></mdatapick>
		</div>
	</div>
</template>

<script>
	import '@/assets/css/reset.less'
	import mcalendar from './mdata'
	import mdatapick from './mdatapick'
	export default {
		data() {
			return {
				//				date0: {
				//					now: "2018-09-05",
				//					end: "2018-09-28",
				//					callback: function(d) {
				//						console.log(d);
				//					}
				//				},
				//				date1: {
				//					now: "2018-11-25",
				//					start: "2018-11-05",
				//					callback: function(d) {
				//						console.log(d);
				//					}
				//				},
				mdatapickdata: {
					start: "2018-09-29",
					end: "2018-10-18"

				}
			}
		},
		methods: {
			callback(startDate, endDate) {
				this.mdatapickdata.start=startDate;
				this.mdatapickdata.end=endDate;
			}
		},
		components: {
			mcalendar,
			mdatapick
		}

	}
</script>
<style>

</style>