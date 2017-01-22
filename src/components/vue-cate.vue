<style>
	.cc-select{
        width: 28%;float: left;
    }
    @media only screen and (min-width: 1200px) {
        .cc-select{
            width: 14%;float: left;
        }
    }
	.select-margin{
		margin-left: 2%;
	}
</style>
<template>
	<select v-model="rc1id" class="cc-select" :class="selectClass" @change="isChanging=true">
		<option value="">选择分类</option>
		<option v-for="rc1id in rc1ids" :value="rc1id[valueType]">{{rc1id.name}}</option>
	</select>
	<select v-show="rc2idshow" v-model="rc2id" class="cc-select select-margin" :class="selectClass" @change="isChanging=true">
		<option value="">选择分类</option>
		<option v-for="rc2id in rc2ids" :value="rc2id[valueType]">{{rc2id.name}}</option>
	</select>
	<select v-show="rc3idshow" v-model="rc3id" class="cc-select select-margin" :class="selectClass" @change="isChanging=true">
		<option value="">选择分类</option>
		<option v-for="rc3id in rc3ids" :value="rc3id[valueType]">{{rc3id.name}}</option>
	</select>
	<slot><slot>
</template>

<script>
	export default {
		ready(){
			this.$http.get(this.catedataurl).then(function (response) {
                this.rc1ids =  response.data.result;
            }, function (error) {
                console.log("系统错误");
            });
			this.oldRc1id = this.rc1id;
			this.oldRc2id = this.rc2id;
			this.oldRc3id = this.rc3id;
			let seletedRc1id = this.rc1ids.filter(function(item){
				return item[this.valueType] == this.rc1id;
			}.bind(this));
			if(seletedRc1id.length){
				this.$set('rc2idshow',true);
				this.$set('rc3idshow',false);
				this.rc2ids = seletedRc1id[0]['child'];
				let seletedRc2id = this.rc2ids.filter(function(item){
					return item[this.valueType] == this.rc2id;
				}.bind(this));
				if(seletedRc2id.length){
					this.rc3ids = seletedRc2id[0]['child'];
				}

			}
		},
		props:{
			rc1id:{
				twoWay: true,
				default:''
			},
			rc2id:{
				twoWay: true,
				default:''
			},
			rc3id:{
				twoWay: true,
				default:''
			},
			selectClass:{
				type:String,
				default:'form-control'
			},
			valueType:{
				type:String,
				default:'id'
			},
			catedataurl:''
		},
		data(){
			return {
				oldRc1id:'',
				oldRc2id:'',
				oldRc3id:'',
				rc1ids:[],
				rc2ids:[],
				rc3ids:[],
				rc2idshow:false,
				rc3idshow:false
			}
		},
		watch:{
			rc1id(){
				let seletedItem = this.rc1ids.filter(function(item){
					return item[this.valueType] == this.rc1id;
				}.bind(this));
				if(seletedItem.length){
					let child = seletedItem[0]['child'];
					if(child.length){
						this.rc2ids = child;
						this.$set('rc2idshow',true);
						this.$set('rc3idshow',false);
						this.rc2id = this.rc2ids[0][this.valueType] == this.oldRc2id ? this.rc2ids[0][this.valueType] : this.oldRc2id;
					}else{
						this.$set('rc2idshow',false);
						this.$set('rc3idshow',false);
						this.rc2id = '';
						this.rc2ids = [];
					}
				}else{
					this.$set('rc2idshow',false);
					this.$set('rc3idshow',false);
					this.rc2id = '';
					this.rc2ids = [];
				}
				this.oldRc2id = this.rc2id;
				this.$dispatch('rc1idChange',seletedItem[0]);
			},
			rc2id(){
				let seletedItem = this.rc2ids.filter(function(item){
					return item[this.valueType] == this.rc2id;
				}.bind(this));
				if(seletedItem.length){
					let child = seletedItem[0]['child'];
					if(child.length){
						this.$set('rc3idshow',true);
						this.rc3ids = child;
						this.rc3id = this.rc3ids[0][this.valueType] == this.oldRc3id ? this.rc3ids[0][this.valueType] : this.oldRc3id;
					}else{
						this.$set('rc3idshow',false);
						this.rc3id = '';
						this.rc3ids = [];
					}
				}else{
					this.$set('rc3idshow',false);
					this.rc3id = '';
					this.rc3ids = [];
				}
				this.oldRc3id = this.rc3id;
				this.$dispatch('rc2idChange', seletedItem[0]);
			},
			rc3id(){
				let seletedItem = this.rc3ids.filter(function(item){
					return item[this.valueType] == this.rc3id;
				}.bind(this));
				this.$dispatch('rc3idChange',seletedItem[0]);
			}
		},
		computed:{

		}
	}
</script>
