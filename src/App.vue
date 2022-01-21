<template>
  <div>
    <a-button @click="isOpen = !isOpen" type="primary">Open</a-button>
  </div>
     <a-modal 
     v-model:visible="isOpen" 
     title="Basic Modal" 
     okText="Save"
     centered
     @ok = "onSave"
     >
    <div>
    <a-checkbox
      style="display:block"
      v-model:checked="checked"
      :indeterminate="indeterminate"
      @change="onCheckAll"
    >
      Select all ({{checkList.size}}/{{options.length}})
    </a-checkbox>
    <a-collapse accordion v-model:activeKey="activeKey">      
        <a-collapse-panel :ref ="option.label" style="backgroundColor:white;" v-for="option in options" :key ="option.label" >
          <template #header>
              <div>{{option.label}} ({{option.checkList.length}}/{{option.children.length}}) </div>
          </template>
          <div>
              <a-checkbox 
              style="display:block"
              v-model:checked= "option.checked"
              :indeterminate = "option.indeterminate"
              @change="onCheckAllEachContent"
              >Select All</a-checkbox>
              <br/>
              <a-checkbox-group v-model:value="option.checkList">
                  <a-row>
                    <a-col v-for="childOp in option.children" :key="childOp.label">
                      <a-checkbox @change="onCheckDetail" :value="childOp.value">{{childOp.label}}</a-checkbox>
                    </a-col>
                    </a-row>
              </a-checkbox-group>
          </div>
        </a-collapse-panel>
    </a-collapse>
  </div>
    </a-modal>
</template>

<script lang="ts">
import { defineComponent,watch,ref,reactive, toRefs} from 'vue'

export default defineComponent({
  setup() {
    const isOpen = ref<boolean>(false);
    const activeKey = ref<string>('');
    const checkAll = reactive({
        checked:false,
        checkList:new Set(),
        indeterminate:false,
      })
  watch(
    ()=> [...checkAll.checkList],
    (checkList)=>{
        checkAll.indeterminate = !!checkList.length && checkList.length < options.length
        checkAll.checked = checkList.length === options.length
    }
  )
      const onCheckAll = (e:any)=>{
        const {checked} = e.target
        if(checked){
          options.forEach((ele)=>{
            checkAll.checkList.add(ele.label)          
            const check = ele.children.map((item)=>item.value)
            ele.checkList = check as unknown as never
            ele.checked = true
            ele.indeterminate = false
            // console.log(ele)
            // console.log(test)
          })
        }else{
          options.forEach((ele)=>{
            checkAll.checkList.delete(ele.label)
            checkAll.checked = false
            checkAll.indeterminate = false
            ele.checkList = []
            ele.checked = false
            ele.indeterminate = false
            })
        // console.log(checkAll)
        }
    
    }

    const onCheckAllEachContent = (e:any)=>{
      const contentNum = activeKey.value
      const {checked} = e.target
      const idx = options.findIndex((item)=>item.label === contentNum)
      Object.assign(options[idx],{
        checkList: checked? options[idx].children.map((ele)=>ele.value) : [],
        checked,
        indeterminate:false
      })
      checked ? checkAll.checkList.add(contentNum): checkAll.checkList.delete(contentNum)

        //  if(checkAll.checkList.size === options.length){
        //   checkAll.indeterminate = false
        //   checkAll.checked  = true
        // }else if(checkAll.checkList.size < options.length && checkAll.checkList.size > 0){
        //   checkAll.indeterminate = true
        //   checkAll.checked  = false
        // } else {
        //   checkAll.indeterminate = false
        // }

    } 
    const  options = reactive([
        {
          label:`Test`,
          checked:false,
          checkList:[],
          indeterminate:false,
          children:[
           {label:'Test00',value:'Test00',checked:false},
           {label:'Test01',value:'Test01',checked:false}
          ]
        },
        {
          label:'Test2',
          checked:false,
          checkList:[],
          indeterminate:false,
          children:[
           {label:'Test10',value:'Test10',checked:false},
           {label:'Test11',value:'Test11',checked:false}
          ]
        },
        {
          label:'Test3',
          checked:false,
          checkList:[],
          indeterminate:false,
          children:[
          {label:'Test20',value:'Test20',checked:false},
          {label:'Test21',value:'Test21',checked:false}
          ]
        }
        ])
   
  const onCheckDetail = (e:any)=>{
    const contentNum = activeKey.value
    const {checked} = e.target
    const idx = options.findIndex((item)=>item.label === contentNum)
    if(checked){
      checkAll.checkList.add(contentNum)
      if((options[idx].checkList.length + 1) === options[idx].children.length){
          options[idx].checked = true
          options[idx].indeterminate = false   
      }else if ((options[idx].checkList.length + 1) < options[idx].children.length ){
          options[idx].checked = false
          options[idx].indeterminate = true     
      }
      return;
    }else{
        if(options[idx].checkList.length === 1){
            checkAll.checkList.delete(contentNum)
        }
        if(options[idx].checkList.length === options[idx].children.length){
          options[idx].checked = false
          options[idx].indeterminate = true 
     
        }else if (options[idx].checkList.length < options[idx].children.length){
            options[idx].indeterminate = false  
        }
        return;
    }
   
  }

 const onSave = ()=>{
  //  console.log(checkAll)
  //  console.log(options)
  //  const onlyChild = options.reduce((acc,item)=>{
  //   // acc.push({[item.label]:item.children.map((ele)=>ele.value)} as unknown as never)
  //   if(acc[item.label]){
  //     acc[item.label] = acc[item.label]
  //   }else{
  //     acc[item.label] = item.children.map((ele)=>ele.value)
  //   }

  //   return acc
  //  },{} as unknown as any)
  //  console.log(onlyChild)
  const onlyChild = options.map((ele)=>ele.children.map((item)=>item.value))
  console.log(onlyChild)
  // const test = onlyChild.map(())
 }

    return {
      isOpen,
      activeKey,
      onCheckAll,
      ...toRefs(checkAll),
      options,
      onCheckAllEachContent,
      onCheckDetail,
      onSave
    }
  },
})
</script>

