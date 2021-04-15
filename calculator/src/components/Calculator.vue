<template>
  <div class = "calculator">
    <div class = "result">{{current || '0'}}</div>
    <div @click = "ac" class = "button init">AC</div>
    <div @click = "choice" class = "button init">+/-</div>
    <div @click = "del" class = "button init">DEL</div>
    <div @click = "operator('/')" class = "button operator">/</div>
    <div @click = "number('7')" class = "button">7</div>
    <div @click = "number('8')" class = "button">8</div>
    <div @click = "number('9')" class = "button">9</div>
    <div @click = "operator('*')" class = "button operator">x</div>
    <div @click = "number('4')" class = "button">4</div>
    <div @click = "number('5')" class = "button">5</div>
    <div @click = "number('6')" class = "button">6</div>
    <div @click = "operator('-')" class = "button operator">-</div>
    <div @click = "number('1')" class = "button">1</div>
    <div @click = "number('2')" class = "button">2</div>
    <div @click = "number('3')" class = "button">3</div>
    <div @click = "operator('+')" class = "button operator">+</div>
    <div @click = "number('0')" class = "button zero">0</div>
    <div @click = "dot" class = "button">.</div>
    <div @click = "cal" class = "button operator">=</div>

  </div>
</template>

<script>
export default {
  data(){
    return{
      current: '',
      ck : 0,//숫자 , 연산자 구분 변수 
      numStack : [],
      operStack : [],
      numTop : -1,
      operTop : -1
    }
  },
  methods: {
    ac(){//all clear
      this.current = ''
      this.numTop = -1;
      this.operTop = -1;
      this.numStack = [];
      this.operStack = [];
      //배열 초기화 하기 
    },
    choice(){//+, - 선택 
      this.current = this.current.charAt(0) === '-' ? this.current.slice(1) : `-${this.current}`;
    },
    del(){//뒤에서부터 하나씩 지우기 
      this.current = this.current.slice(0,-1);
    },
    dot(){
      if(this.current.indexOf('.') == -1) this.current += '.';
    },
    number(num){
      console.log(this.operTop);
      console.log(this.numTop);
      if(this.ck){//연산자 입력되어있음
        if(this.operTop != -1){//스택이 비어있지 않을때 
          var currentPriority = this.priority(this.current);//현재연산자 우선수위 받아오기 
          var stackPriority = this.priority(this.operStack[this.operTop]);//스택 안에 연산자 우선순위 받아오기
          console.log(currentPriority, stackPriority);
          if(currentPriority >= stackPriority){//스택 안 연산자보다 우선순위가 크거나 같으면 push
            this.push(0,this.current);
          }
          else{//스택안 연산자보다 우선순위가 작음 => pop
            var operTmp = this.operStack[this.operTop] ;
            this.operTop --;

            var numTmp1 = parseFloat(this.numStack[this.numTop-1]);
            var numTmp2 = parseFloat(this.numStack[this.numTop]);
            this.numTop = this.numTop-2;

            this.push(1,this.calculation(numTmp1,numTmp2,operTmp));//계산 결과 numStack push

            this.push(0,this.current);//현재 연산자 operStack push

          }
        }
        else{//스택이 비어있음 => push
          this.push(0,this.current);
        }
        
        this.ck = 0;
        this.current = num;
      }
      else this.current += num;
    },
    operator(operate){//연산자 클릭
      //연산자 누르기 전 숫자 numStack에 집어넣기
      if(!this.ck){//연산자 처음 누름 
        this.push(1,this.current);
        this.ck = 1;
      }
      this.current = operate;
    },
    cal(){// = 클릭
      //스택에 남은 식 계산 
      if(this.current !='+' && this.current != '*' && this.current !="/"){
        this.push(1,this.current);//마지막 숫자 stack에 push 
        
        while(this.numTop != 0){
        var operTmp = this.operStack[this.operTop] ;
        this.operTop --;

        var numTmp1 = parseFloat(this.numStack[this.numTop-1]);
        var numTmp2 = parseFloat(this.numStack[this.numTop]);
        this.numTop = this.numTop-2;
 
        this.push(1,this.calculation(numTmp1,numTmp2,operTmp));//계산 결과 numStack push
        }

        this.current = String(this.calculation(numTmp1,numTmp2,operTmp));//계산 결과 화면에 표시해주기 
        this.numTop --;
      }

      console.log(this.operStack);
      console.log(this.numStack);
      console.log("oper : "+this.operTop+"num : "+this.numTop);
    },
    priority(oper){//우선순위 지정
      switch(oper){
        case'/':
        case'*':
          return 5;
        case'+':
        case'-':
          return 2;
      }
    },
    push(select,result){//stack push
      if(select){//numStack
        this.numTop ++;
        this.numStack[this.numTop] = result;
      }
      else{//operStack
        this.operTop ++;
        this.operStack[this.operTop] = result;
      }
    },
    calculation(num1,num2,oper){//스택에 남아있는 수식 계산하기 
      switch (oper) {
        case '/':
          return num1/num2;
        case '*':
          return num1 *  num2;
        case '+':
          return num1 + num2;
        case '-':
          return num1 - num2;
      }
    }



  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.calculator{
  margin: 20%;
  width: 350px;
  height: 400px;
  font-size: 33px;
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-auto-rows: minmax(50px, auto);
  border: 3px solid rgb(247, 127, 167);
  border-radius: 1em;
  
}

.result{
  grid-column: 1 / 5;
  border:  palevioletred;
  margin-top: 5%;
  margin-bottom: 8%;
  margin-left: 70%;
}

.button{
  background-color: pink;
  border-radius: 1em;
  margin: 5%;
  color: aliceblue;
  padding: 7px 10px;
  text-align: center;
}

.zero{
  grid-column: 1 / 3;
  border-radius: 1em;
}

.operator{
  background-color: plum;
  color: white;
  border-radius: 1em;
}

.init{
  background-color: thistle;
}

</style>
