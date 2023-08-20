<template>
  <div>
    <BaseCard title="This is the dummy title" subtitle="this is dummy subtitle" modalid="my-modal">

        <template v-slot:highlight>
          &lt;b-alert show variant=&quot;primary&quot;&gt;Primary Alert&lt;/b-alert&gt;
        </template> 
    
        <template v-slot:comcode>

        </template> 
   
    </BaseCard>

  </div>  
</template>

<script>
import BaseCard from '../components/card/BaseCard'
export default {
  name: "Starter",
  components:{
    BaseCard
  },
  data: () => ({
    
  })
};
</script>
