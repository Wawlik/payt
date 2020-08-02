<template>
	<div class="list">
		<div id="report">
			<div class="date">
				<div>
					<span>Сегодня</span>
				</div>
				<ul>
					<li class="transaction">
						<details>
							<summary>
								<div class="order">
									Заказ:
									<b>89201</b>
								</div>
								<div class="time">
									<time datetime="2019-04-18T16:45">16:45</time>
								</div>
								<div class="account">4672 **** **** 4892</div>
								<div class="amount rubles positive">12 380</div>
							</summary>
							<p class="description">Ультрафиолетовый блокнот рокового кролика с зелёными ушами</p>
						</details>
					</li>

					<li class="transaction">
						<details>
							<summary>
								<div class="order">
									Перевод:
									<b>49023</b>
								</div>
								<div class="time">
									<time datetime="2019-04-18T16:45">14:30</time>
								</div>
								<div class="account">6823 **** **** 7382</div>
								<div class="amount rubles positive">32 560</div>
							</summary>
							<p class="description">Ультрафиолетовый блокнот рокового кролика с зелёными ушами</p>
						</details>
					</li>

					<li class="transaction">
						<details>
							<summary>
								<div class="order">
									Заказ:
									<b>16359</b>
								</div>
								<div class="time">
									<time datetime="2019-04-18T16:45">03:20</time>
								</div>
								<div class="account">2782 **** **** 8291</div>
								<div class="amount rubles positive">990</div>
							</summary>
							<p class="description">Ультрафиолетовый блокнот рокового кролика с зелёными ушами</p>
						</details>
					</li>
				</ul>
			</div>

			<div class="date">
				<div>
					<span>Вчера</span>
				</div>
				<ul>
					<li class="transaction">
						<details>
							<summary>
								<div class="order">
									Вывод:
									<b>90274</b>
								</div>
								<div class="time">
									<time datetime="2019-04-18T16:45">18:15</time>
								</div>
								<div class="account">4412 **** **** 5682</div>
								<div class="amount rubles negative">116 200</div>
							</summary>
							<p class="description">Ультрафиолетовый блокнот рокового кролика с зелёными ушами</p>
						</details>
					</li>
					<li class="transaction">
						<details>
							<summary>
								<div class="order">
									Заказ:
									<b>46791</b>
								</div>
								<div class="time">
									<time datetime="2019-04-18T16:45">12:30</time>
								</div>
								<div class="account">4289 **** **** 2839</div>
								<div class="amount rubles positive">8 620</div>
							</summary>
							<p class="description">Ультрафиолетовый блокнот рокового кролика с зелёными ушами</p>
						</details>
					</li>
				</ul>
			</div>

			<div class="date">
				<div>
					<span>18.04.19</span>
				</div>
				<ul>
					<li class="transaction">
						<details>
							<summary>
								<div class="order">
									Счёт:
									<b>13089</b>
								</div>
								<div class="time">
									<time datetime="2019-04-18T16:45">14:20</time>
								</div>
								<div class="account">nikita.s.doroshenko@gmail.com</div>
								<div class="amount rubles positive">16 000</div>
							</summary>
							<p class="description">Ультрафиолетовый блокнот рокового кролика с зелёными ушами</p>
						</details>
					</li>
					<li class="transaction">
						<details>
							<summary>
								<div class="order">
									Перевод:
									<b>76982</b>
								</div>
								<div class="time">
									<time datetime="2019-04-18T16:45">08:55</time>
								</div>
								<div class="account">4689 **** **** 2102</div>
								<div class="amount rubles positive">840</div>
							</summary>
							<p class="description">Ультрафиолетовый блокнот рокового кролика с зелёными ушами</p>
						</details>
					</li>
					<li class="css-2801">
						<details>
							<summary>
								<div class="order">
									Заказ:
									<b>97623</b>
								</div>
								<div class="time">
									<time datetime="2019-04-18T16:45">02:20</time>
								</div>
								<div class="account">5672 **** **** 6728</div>
								<div class="amount rubles positive">6 430</div>
							</summary>
							<p class="description">Ультрафиолетовый блокнот рокового кролика с зелёными ушами</p>
						</details>
					</li>
				</ul>
			</div>
		</div>
	</div>
</template>

<script>
import axios from "axios";

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
					// console.log({ res2 });
					this.trList = res2.data?.result ?? null;
					this.sortListByDate(res2.data?.result);
					// console.log('this.trList=', this.trList);
				})
				.catch((err1) => {
					console.error({ err1 });
				});
		},
		sortListByDate(list){
			let misc = JSON.parse(JSON.stringify(list)); // copy obj
			// no date case (undef)
			misc.sort((a,b) => {
				if(new Date(b.updated_at) > new Date(a.updated_at)){
					return 1;
				}
				if(new Date(b.updated_at) < new Date(a.updated_at)){
					return -1;
				}
				return 0;
				// return new Date(b.date) - new Date(a.date);
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
				console.log((new Date(oDate)).toDateString());
				// let nextDate = misc[one+1]?.updated_at;
				// console.log(new Date(oDate) > new Date(nextDate))
				if(pDate){
					// console.log(pDate});
					if(pDate !== (new Date(oDate)).toDateString()){
						bp.push(one + 1);
						pDate = (new Date(oDate)).toDateString();
					}
				} else {
					pDate = (new Date(oDate)).toDateString();
				}
			}
			console.log({bp});
			// splitting
			let subList = [];
			for(let one = 0; one < bp.length; one++){
				subList.push(misc.splice(0, bp[one]));
			}
			console.log({subList});
		}
	},
	async mounted() {
		await this.getData();
	},
};
</script>

<style>
h3 {
	margin: 40px 0 0;
}
ul {
	list-style-type: none;
	padding: 0;
}
li {
	display: inline-block;
	margin: 0 10px;
}
a {
	color: #42b983;
}
</style>
