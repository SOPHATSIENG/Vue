<template>
  <div class="flex justify-center items-center min-h-screen bg-gray-50 p-4">
    <div class="w-full max-w-md bg-white rounded-2xl shadow-sm border border-gray-100 overflow-hidden">
      
      <div class="bg-gray-900 p-6 text-center">
        <span class="text-xs font-bold tracking-widest text-teal-400 uppercase block mb-1">
          Performance Dashboard
        </span>
        <h2 class="text-xl font-bold text-white">
          Student Tracking Score
        </h2>
      </div>

      <div class="p-6 space-y-6">
        
        <div class="flex justify-between items-center pb-4 border-b border-gray-100">
          <div>
            <span class="text-xs font-semibold text-gray-400 uppercase tracking-wider block">
              Student Name
            </span>
            <span class="text-lg font-bold text-gray-700">
              {{ studentName }}
            </span>
          </div>
          
          <span 
            :class="average >= passMark ? 'bg-emerald-50 text-emerald-700 border-emerald-200' : 'bg-rose-50 text-rose-700 border-rose-200'"
            class="px-3 py-1 text-sm font-semibold rounded-full border shadow-xs"
          >
            {{ average >= passMark ? 'Pass' : 'Fail' }}
          </span>
        </div>

        <div class="grid grid-cols-2 gap-4 text-center">
          
          <div class="border-r border-gray-100 py-2">
            <span class="text-xs font-bold text-gray-400 uppercase tracking-wider block mb-1">
              Total Score
            </span>
            <span class="text-3xl font-extrabold text-gray-900">
              {{ totalScore }}
            </span>
          </div>
          
          <div class="py-2">
            <span class="text-xs font-bold text-gray-400 uppercase tracking-wider block mb-1">
              Average Score
            </span>
            <span class="text-3xl font-extrabold text-indigo-600">
              {{ average.toFixed(2) }}%
            </span>
          </div>

        </div>

      </div>

      <div class="h-1.5 bg-teal-400"></div>
      
    </div>
  </div>
</template>

<script setup>
    import { ref, computed } from "vue";
    const studentName = ref("Sieng Sophat")
    let score = ref([
        {subject: "English", score: 90},
        {subject: "Vue", score: 90},
        {subject: "Node.js", score: 70},
    ]);

    const passMark = ref(50);
    const totalScore =computed( () =>{
        let total = 0;
        score.value.forEach(item =>{
            total += item.score
        });
        return total;

    })

    const average = computed( () => {
        return totalScore.value === 0 ? 0 : totalScore.value/score.value.length;
    })

    const examStatuse = computed ( ()=> {
        if (passMark.value < average.value){
            return "pass"
        }
        return "failed"
    })
    


</script>
