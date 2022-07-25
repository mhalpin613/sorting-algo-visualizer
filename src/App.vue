<template>
  <body>
    <header @click="hideMenu()">Sorting Algorithm Visualizer</header>
    <div class="options-container">
      <div class="dropdown">
        <button class="dropbtn" @click="showMenu()">Algorithms></button>
        <div class="dropdown-content">
          <a href="#" @click="bubbleSort()">Bubble Sort</a>
          <a href="#" @click="selectionSort()">Selection Sort</a>
          <a href="#" @click="insertionSort()">Insertion Sort</a>
          <a href="#" @click="mergeSort()">Merge Sort</a>
          <a href="#" @click="quickSort()">Quick Sort</a>
          <a href="#" @click="heapSort(data)">Heap Sort</a>
        </div>
      </div>
    <button class="shuffle" @click="shuffle()">Shuffle</button>
    </div>
    <div class="display-contain">
      <div class="display-box" @click="hideMenu()">
        <div class="bar" v-for="(number, index) in this.data" :key="index" :style="{height: number + 'px', backgroundColor: 'limegreen'}"></div>
      </div>
    </div>
  </body>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      data: [],
      size: 125,
    }
  },
  components: {
  },
  methods: {

    hideMenu() {
      document.getElementsByClassName("dropdown-content")[0].style.display = "none";
    },

    showMenu() {
      document.getElementsByClassName("dropdown-content")[0].style.display = "block";
    },
    
    shuffle() {
      this.data = [];
      for (let i = 0; i < this.size; i++) {
        this.data.push(Math.floor(Math.random() * (700 - 50 + 1) ) + 50);
      }
    },

    pause(milliseconds) {
      return new Promise(resolve => setTimeout(resolve, milliseconds));
    },

    async bubbleSort() {

      for (let i = 0; i < this.data.length; i++) {

        for (let j = 0; j < (this.data.length - i - 1); j++) {

          if (this.data[j] > this.data[j + 1]) {

            // swap elements and delay to show the swap
            [this.data[j], this.data[j + 1]] = [this.data[j + 1], this.data[j]];
            await this.pause(10);
          }
        }
      }
    },

    async selectionSort() {

      for (let i = 0; i < this.data.length - 1; i++) {
        let maxIndex = i;

        for (let j = i + 1; j < this.data.length; j++) {

          // find the max index
          if (this.data[j] < this.data[maxIndex]) {
            maxIndex = j;
          }
        }
        // swap the data and delay to show the swap
        await this.pause(200);
        [this.data[i], this.data[maxIndex]] = [this.data[maxIndex], this.data[i]];
      }
    },

    async insertionSort() {

      for (let i = 1; i < this.data.length; i++) {
        let j = i;

        while (j > 0 && this.data[j] < this.data[j - 1]) {

          // swap and delay to visualize
          [this.data[j - 1], this.data[j]] = [this.data[j], this.data[j - 1]];
          j--;
          await this.pause(10);
        }
      }
    },
    
    /**
     * I decided to use the iterative approach to merge sort for visualization
     * purposes, definetely not because of readibility and consicesness :/
     * The time complexity is still O(N log N) because of the *2 for each
     * iteration in the outside for loop. ex. for an array with 20 elements:
     * size = 1, 2, 4, 8, 16 (5 iterations instead of 19) => not N^2
     */
    async mergeSort() {

      // array that holds sorted values
      let helpArray = []

      for (let size = 1; size < this.data.length; size *= 2) {

        for (let leftStart = 0; leftStart < this.data.length; leftStart += 2 * size) {

          // finding left and right starting points
          let left = leftStart;
          let right = Math.min(left + size, this.data.length);

          // finding left and right end points
          let leftMax = right;
          let rightMax = Math.min(right + size, this.data.length);

          let i = left;

          // swapping and sorting elements
          while (left < leftMax && right < rightMax) {

            if (this.data[right] >= this.data[left]) {
              helpArray[i++] = this.data[left++];
            } else {
              helpArray[i++] = this.data[right++];
            }
          }

          // keeping up with any left over elements
          while (left < leftMax) {
            helpArray[i++] = this.data[left++];
          }

          while (right < rightMax) {
            helpArray[i++] = this.data[right++];
          }
        }

        // delay
        await this.pause(300);

        // updating the main array and "resetting" the helper array
        let temp = this.data;
        this.data = helpArray;
        helpArray = temp;
      }
    },

    async partition(low, high) {

      // getting high pivot
      let pivot = this.data[high];
    
      // index of smaller element
      let i = (low - 1);
      for (let j = low; j <= high - 1; j++) {

        // If current element is smaller than or equal to pivot
        if (this.data[j] <= pivot) {
          i++;

          // swap and delay
          await this.pause(12); 
          [this.data[i], this.data[j]] = [this.data[j], this.data[i]];
        }
      }
    
      // swap 
      [this.data[i + 1], this.data[high]] = [this.data[high], this.data[i + 1]];
      return i + 1;
    },

    /**
     * Used iterative version of quick sort to better visualize it, todo is to eventually
     * see if I can replace with the recursive quicksort
     */
    async quickSort() {

      let low = 0;
      let high = this.data.length - 1

      // Create a helper stack
      let stack = new Array(high - low + 1);
      stack.fill(0);
   
      // initialize top of stack
      let top = -1;
   
      // push initial values of low and high
      stack[++top] = low;
      stack[++top] = high;
   
      // Keep popping from stack while there are elements
      while (top >= 0) {

        // Pop high and low
        high = stack[top--];
        low = stack[top--];
   
        // Set pivot 
        let pivot = await this.partition(low, high);
   
        // If there are elements on left side of pivot push left side to stack
        if (pivot - 1 > low) {
          stack[++top] = low;
          stack[++top] = pivot - 1;
        }
   
        // If there are elements on right side of pivot then push right side to stack
        if (pivot + 1 < high) {
          stack[++top] = pivot + 1;
          stack[++top] = high;
        }
      }
    },

    async heapify(array, size, i) {

      // delay to visualize and make max the root
      await this.pause(10)
      let max = i
      let left = 2 * i + 1
      let right = 2 * i + 2

      // if left is larger than root
      if (left < size && array[left] > array[max])
        max = left

      // if right is larger than max
      if (right < size && array[right] > array[max])
        max = right

      // if max is not root
      if (max != i) {

        // swap
        [array[max], array[i]] = [array[i], array[max]];

        // heapify the affected sub-tree
        await this.heapify(array, size, max)
      }
    },

    async heapSort(array) {

      let size = array.length

      // build heapSort (rearrange array)
      for (let i = Math.floor(size / 2 - 1); i >= 0; i--) {
        await this.heapify(array, size, i)
      }

      // one by one extract an element from heapSort
      for (let i = size - 1; i >= 0; i--) {

        // move current to end
        [array[i], array[0]] = [array[0], array[i]];
        await this.heapify(array, i, 0)
      }
    },
  },
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Edu+SA+Beginner:wght@500&family=Press+Start+2P&display=swap');

body { 
  margin: 0; 
  font-family: 'Press Start 2P','tahoma';
  background-color: black;
  text-shadow: 0 1px 0 #595c58, 0 2px 0 #5b605b, 0 3px 0 #6c706b;
  height: 100%;
  overflow: hidden;
}

header {
  text-align: center;
  font-style: italic;
  font-size: 5vh;
  margin: 3vh 0 3vh 0;
  color: limegreen;
}

.options-container {
  display: flex;
  justify-content: space-around;
}

.dropbtn {
  background-color: limegreen;
  color: #113f67;
  padding: 16px;
  font-size: 16px;
  border: none;
  border-radius: 10px;
  text-shadow: 0 2px 0 #b2c4b1;
  border: 10px inset #00ff00;
  font-family: 'Press Start 2P','tahoma';
  transition: 100ms;
}

.dropbtn:hover {
  transform: scale(1.05);
  cursor: pointer;
  border: 10px outset #00ff00;
  background-color: white;
}

.dropdown {
  position: relative;
  display: inline-block;
}

.dropdown-content {
  display: none;
  position: absolute;
  background-color: #f1f1f1;
  min-width: 160px;
  box-shadow: 0px 8px 16px 0px rgba(0,0,0,0.2);
  z-index: 1;
}

.dropdown-content a {
  color: black;
  background-color: limegreen;
  text-shadow: 0 2px 0 #b2c4b1;
  padding: 12px 16px;
  text-decoration: none;
  display: block;
}

.dropdown-content a:hover {
  background-color: white;
}

.dropdown:hover .dropdown-content {
  display: block;
}

.shuffle {
  width: 15vw;
  max-width: 150px;
  min-width: 150px;
  border-radius: 10px;
  border: none;
  font-size: small;
  font-weight: 600;
  background-color: limegreen;
  color: #113f67;
  transition: 100ms;
  text-shadow: 0 2px 0 #b2c4b1;
  border: 10px inset #00ff00;
  font-family: 'Press Start 2P','tahoma';
}

.shuffle:hover {
  transform: scale(1.1);
  cursor: pointer;
  background-color: white;
  border: 10px outset #00ff00;
}

.display-contain {
  display: flex;
  justify-content: center;
  align-items: center;
}

.display-box {
  display: flex;
  margin: 0 5vw 0 5vw;
  justify-content: center;
  height: 750px;
  min-height: 500px;
  width: fit-content;
  transform:  rotateX(180deg);
}

.bar {
  width: .5vw;
  background-color: limegreen;
  margin-right: 2px;
  border-radius: 50px;
}

@media screen and (min-device-width: 1200px) and (max-device-width: 1600px) { 
  .display-box {
    translate: (-75px, 0);
  }
}

@media only screen and (min-device-width: 375px) and (max-device-width: 812px)  { 
  body {
    overflow: auto;
  }
}


</style>
