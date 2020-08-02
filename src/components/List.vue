<template>
	<div class="list">
		<div id="report">
			<div class="date" v-for="(one) in trList" :key="'transactions-on-' + (new Date(one[0].updated_at)).toDateString()">
				<div>
					<span>{{getGroupDate(one[0].updated_at)}}</span>
				</div>
				<ul>
					<li class="transaction" v-for="(two) in one" :key="two.event_id">
						<details>
							<summary>
								<div class="order">
									{{getType(two.type)}}:
									<b>{{two.event_id}}</b>
									<!-- <b>{{two.bill_id}}</b> -->
								</div>
								<div class="time">
									<time :datetime="two.updated_at">
										{{(new Date(one[0].updated_at)).toLocaleTimeString('ru',{minute: '2-digit', hour:'2-digit'})}}
									</time>
								</div>
								<div class="account">{{getContacts(two)}}</div>
								<div :class="'amount ' + (getCurrency(two.currency)) + (true ? ' positive ' : ' negative')">{{two.amount}}</div>
							</summary>
							<p v-if="two.description" class="description">{{two.description}}</p>
						</details>
					</li>
				</ul>
			</div>

		</div>
	</div>
</template>

<script>
import axios from "axios";
import Vue from "vue";

export default {
	name: "list",
	data(){
		return {
			trList: null
		}
	},
	methods:{
		getData(){
			const url =
				"https://api.stage.capusta.space/v1/cabinet/protected/transactions/page/1";
			const config = {
				headers: { Authorization: "Bearer 5d370094-57a7-4476-ace4-4f29bfa43d44" }
			};
			axios.get(url, config)
				.then((res2) => {
					// console.log('res2.data?.result=', res2.data?.result)
					this.sortListByDate(res2.data?.result);
				})
				.catch((err1) => {
					console.error({ err1 });
				});
		},
		sortListByDate(list){
			let misc = JSON.parse(JSON.stringify(list)); // copy obj
			// ++no date case (undef)
			misc.sort((a,b) => {
				if(new Date(b.updated_at) > new Date(a.updated_at)){
					return 1;
				}
				if(new Date(b.updated_at) < new Date(a.updated_at)){
					return -1;
				}
				return 0;
			})
			// console.log({misc});
			this.splitListByDay(misc);
		},
		splitListByDay(list){
			let misc = JSON.parse(JSON.stringify(list));
			let pDate = null;
			let bp = [];
			for(let one = 0; one < misc.length; one ++){
				let oDate = misc[one].updated_at;
				// console.log((new Date(oDate)).toDateString());
				if(pDate){
					if(pDate !== (new Date(oDate)).toDateString()){
						bp.push(one); // set breakpoint
						pDate = (new Date(oDate)).toDateString();
					}
				} else {
					pDate = (new Date(oDate)).toDateString();
				}
			}
			// splitting
			let subList = [];
			let dec = 0;
			for(let one = 0; one < bp.length; one++){
				subList.push(misc.splice(0, bp[one] - dec));
				dec += bp[one] - dec;
			}
			subList.push(misc); // push rest
			Vue.set(this, 'trList', subList);
		},
		getGroupDate(date){
			const today = new Date();
			const ytd = new Date(today.setDate(today.getDate() - 1));
			const date2 = new Date(date);

			let dd = String(today.getDate()).padStart(2, '0');
			let mm = String(today.getMonth() + 1).padStart(2, '0');
			let yy = today.getFullYear();
			
			let ddy = String(ytd.getDate()).padStart(2, '0');
			let mmy = String(ytd.getMonth() + 1).padStart(2, '0');
			let yyy = ytd.getFullYear();

			let dd2 = String(date2.getDate()).padStart(2, '0');
			let mm2 = String(date2.getMonth() + 1).padStart(2, '0');
			let yy2 = date2.getFullYear();
			
			if(dd === dd2 && mm == mm2 && yy === yy2){
				return 'Сегодня';
			} else if( ddy === dd2 && mmy == mm2 && yyy === yy2){
				return 'Вчера';
			}
			return date2.toLocaleDateString('ru', {day: '2-digit', month: '2-digit', year: '2-digit'});
		},
		getCurrency(cur){
			return cur === 'RUB' ? ' rubles ' : ' USD ';
		},
		getContacts(tr){
			return tr.account_number ? 
			( tr.account_number.slice(0,4) + ' **** **** ' + tr.account_number.slice(-4) )
			: tr.senderProperties?.email || '-';
		},
		getType(type){
			let res = 'Транзакция';
			switch (type) {
				case "REFUND":
					res = 'Возврат'
					break;
			
				case "PURCHASE_BILL":
					res = 'Счет'
					break;
			
				case "PURCHASE":
					res = 'Заказ'
					break;
			
				default:
					res = 'Транзакция'
					break;
			}
			return res;
		}
	},
	async mounted() {
		await this.getData();
	},
};
</script>
