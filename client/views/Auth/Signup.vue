<template>
<div class="tab-pane active" id="panel1" role="tabpanel">
	<div class="card-block">
		<!--Body-->
		<h4 class="h4-responsive my-1">Create a new account</h4>
		<div class="btn-group btn-switch" data-toggle="buttons">
			<label class="btn btn-primary" :class="{active: (user.role=='COMPANY')}" @click.stopPropagation="setRole_Company">
				<input type="radio" name="options" id="option1" autocomplete="off" :checked="user.role=='COMPANY'" ><i class="material-icons">domain</i><br>Company
			</label>
			<label class="btn btn-primary" :class="{active: (user.role=='STUDENT' || user.role=='STAFF')}" @click.stopPropagation="setRole_Student">
				<input type="radio" name="options" id="option2" autocomplete="off" :checked="user.role=='STUDENT'" ><i class="material-icons">school</i><br>University
			</label>
		</div>
		<br>
		<form v-on:submit.prevent.stop="submitsignup">
			
			<div class="md-form my-1">
				<i class="material-icons prefix">face</i>
				<input type="text" id="formSignup_1" class="form-control" v-model="user.name">
				<label for="formSignup_1">{{(this.user.role == 'STUDENT' || this.user.role == 'STAFF')?"LDAP Username ('eg: exxxxx')":'Your name'}}</label>
			</div>
			<div class="md-form my-1" v-if="user.role == 'COMPANY'">
				<i class="material-icons prefix">email</i>
				<input type="email" id="formSignup_2" class="form-control" v-model="user.email" name="email">
				<label for="formSignup_2">Your email</label>
			</div>
			<div class="md-form my-1">
				<i class="material-icons prefix">lock</i>
				<input type="password" id="formSignup_3" class="form-control" v-model="user.password">
				<label for="formSignup_3">{{(this.user.role == 'STUDENT' || this.user.role == 'STAFF')?"LDAP Password":'Password'}}</label>
			</div>
			<div class="text-center">
				<button type="submit" class="btn btn-block btn-primary flex-center"><i class="material-icons">face</i> <span class="flex-center">Create a new account</span></button>
			</div>
		</form>
	</div>
</div>
</template>
<script>
	
import Vue from 'vue';
import store from 'store';

export default {
	data: () => {
		return {
			user: {
				name: '',
				role: 'COMPANY',
				email: '',
				password: '',
			}
		}
	},
	methods: {
		submitsignup: function() {
			if(this.user.role == 'STUDENT' || this.user.role == 'STAFF'){
				Vue.auth.signupLDAP(this.user);
				return;
			}
			Vue.auth.signup(this.user);
		},
		setRole_Company: function(){
			this.user.role = 'COMPANY';
		},
		setRole_Student: function(){
			this.user.role = 'STUDENT';
		},
	},
	watch: {
		user: {
			handler: function(_user){
				// console.log(_user);

				if(_user.name == '' || _user.role == 'COMPANY'){
					return;
				}
				if(_user.name.match(/^e[0-9]{5}$/g)){
					if(this.user.role != 'STUDENT'){
						store.dispatch('showMessage', 'Switching to student account')
						this.user.role = 'STUDENT';
					}
				} else {
					if(this.user.role != 'STAFF'){
						if(_user.name.length>=6) store.dispatch('showMessage', 'Switching to staff account')
						this.user.role = 'STAFF';
					}
				}
			},
			deep: true,
		}
	},
	mounted: function(){
		this.setRole_Student()
	}
}
</script>