<template>
  <div>
    <menu-category/>
    <menu-c/>
    <v-row>
      <v-col>
        <order/>
      </v-col>
      <v-col>
        <count/>
      </v-col>
    </v-row>
  </div>
</template>
  
<script>
import MenuC from '../components/Menu.vue'
import Order from '@/components/Order.vue'
import Count from '@/components/Count.vue'
import menuCategory from '@/components/menuCategory.vue'
import axios from 'axios';
import { EventBus } from '../main.js';

export default {
  name: 'App',
  components: {
    MenuC,
    Order,
    Count,
    menuCategory
  },
  data() {
    return {
      recognition: null,
    };
  },
  created(){
    //this.speak('원하시는 메뉴를 선택하세요');
  },
  mounted() {
    this.startRecognition();
  },
  methods: {
    async speechItem(item, cnt = 1) {
        const response = await axios.get(`http://localhost:8000/price/${item}`);
        const data = { p_name: item, p_price: response.data };
        EventBus.$emit('add-to-cart', data);
        console.log('cnt:', cnt);
        EventBus.$emit('itemCount', { name: item, count: cnt - 1 });
        this.speak(`${item} ${cnt}개를 담았습니다.`);
    },
    speak(message) {
      const utterance = new SpeechSynthesisUtterance(message);
      utterance.lang = 'ko-KR';
      window.speechSynthesis.speak(utterance);
    },
    startRecognition() {
      this.recognition = new (window.SpeechRecognition || window.webkitSpeechRecognition)();
      this.recognition.lang = 'ko-KR';
      this.recognition.start();

      this.recognition.onresult = async (event) => {
        const speechResult = event.results[0][0].transcript.trim();
        console.log('Result:', speechResult);

        // 개수 추출 로직
        let count = 1;  // 기본 개수는 1
        const numberMapping = { 
            '한': 1, '두': 2, '세': 3, '네': 4, '다섯': 5, '여섯': 6, 
            '일곱': 7, '여덟': 8, '아홉': 9, '열': 10 
        };

        // 숫자가 포함된 경우
        const numberMatch = speechResult.match(/\d+/);
          if (numberMatch) {
              count = parseInt(numberMatch[0], 10);
          } else {
              // 한글 표현으로 개수 파악
              Object.keys(numberMapping).forEach(key => {
                  if (speechResult.includes(key + ' 개') || speechResult.includes(key + '개')) {
                      count = numberMapping[key];
                  }
              });
          }

          if (speechResult.includes('단품') || speechResult.includes('햄버거 단품') || speechResult.includes('단풍')) {
            EventBus.$emit('sendMenuCategory', 'burger')
          } else if (speechResult.includes('사이드')) {
            EventBus.$emit('sendMenuCategory', 'side')
          } else if (speechResult.includes('음료') || speechResult.includes('음요')) {
            EventBus.$emit('sendMenuCategory', 'drink')
          } else if (speechResult.includes('결제') || speechResult.includes('골랐어')) {
            EventBus.$emit('openWindow')
          } else if (speechResult.includes('전체 삭제')) {
            EventBus.$emit('deleteOrder')
          } else if (speechResult.includes('이전')) {
            EventBus.$emit('prev')
          } else if (speechResult.includes('다음')) {
            EventBus.$emit('next')
        } else if (speechResult.includes('더블 불고기 버거 세트') || speechResult.includes('더블불고기버거세트')) {
          this.speechItem('더블 불고기 버거 세트', count)
        } else if (speechResult.includes('슈슈 버거 세트') || speechResult.includes('슈슈버거세트') || speechResult.includes('슈슈버거 세트')) {
          this.speechItem('슈슈 버거 세트', count)
        } else if (speechResult.includes('더블 빅맥 세트') || speechResult.includes('더블빅맥 세트') || speechResult.includes('더블빅맥세트')) {
          this.speechItem('더블 빅맥세트', count);
        } else if (speechResult.includes('빅맥 BLT 세트') || speechResult.includes('빅맥BLT세트') || speechResult.includes('빅맥BLT 세트')) {
          this.speechItem('빅맥® BLT 세트', count);
        } else if (speechResult.includes('더블 쿼터파운더 치즈 세트') || speechResult.includes('더블쿼터파운더치즈 세트') || speechResult.includes('더블쿼터파운더치즈세트')) {
          this.speechItem('더블 쿼터파운더® 치즈 세트', count);
        } else if (speechResult.includes('쿼터파운더 치즈 세트') || speechResult.includes('쿼터파운더치즈 세트') || speechResult.includes('쿼터파운더치즈세트')) {
          this.speechItem('쿼터파운더® 치즈 세트', count);
        } else if (speechResult.includes('맥 크리스피 디럭스 버거 세트') || speechResult.includes('맥 크리스피 디럭스버거 세트') || speechResult.includes('맥크리스피디럭스버거세트')) {
          this.speechItem('맥크리스피™ 디럭스 버거 세트', count);
        } else if (speechResult.includes('맥 크리스피 클래식 버거 세트') || speechResult.includes('맥크리스피클래식버거 세트') || speechResult.includes('맥크리스피클래식버거세트')) {
          this.speechItem('맥크리스피 클래식 버거 세트', count);
        } else if (speechResult.includes('트리플 치즈 버거 세트') || speechResult.includes('트리플치즈버거 세트') || speechResult.includes('트리플 치즈 세트')) {
          this.speechItem('트리플 치즈버거 세트', count);
        } else if (speechResult.includes('맥 스파이시 상하이 버거 세트')) {
          this.speechItem('맥스파이시 상하이 버거 세트', count);
        } else if (speechResult.includes('1955 버거 세트') || speechResult.includes('195 버거 세트') || speechResult.includes('195버거 세트')) {
          this.speechItem('1955 버거 세트');
        } else if (speechResult.includes('맥 치킨 모짜렐라 세트') || speechResult.includes('맥치킨 모짜렐라 세트') || speechResult.includes('맥 치킨 모짜렐라 세트')) {
          this.speechItem('맥치킨 모짜렐라 세트', count);
        } else if (speechResult.includes('맥 치킨 세트')) {
          this.speechItem('맥치킨 세트', count);
        } else if (speechResult.includes('에그 불고기 버거 세트')) {
          this.speechItem('에그 불고기 버거 세트', count);
        } else if (speechResult.includes('불고기 버거 세트')) {
          this.speechItem('불고기 버거 세트', count);
        } else if (speechResult.includes('슈비 버거 세트') || speechResult.includes('슈비버거 세트') || speechResult.includes('슈비버거세트')) {
          this.speechItem('슈비 버거 세트', count);
        } else if (speechResult.includes('베이컨 토마토 디럭스 세트') || speechResult.includes('베이컨토마토디럭스 세트') || speechResult.includes('베이컨토마토디럭스세트')) {
          this.speechItem('베이컨 토마토 디럭스 세트', count);
        } else if (speechResult.includes('더블 치즈버거 세트') || speechResult.includes('더블치즈버거 세트')) {
          this.speechItem('더블 치즈버거 세트', count);
        } else if (speechResult.includes('치즈버거 세트')) {
          this.speechItem('치즈버거 세트', count);
        } else if (speechResult.includes('빅맥 세트')) {
          this.speechItem('빅맥® 세트', count);
          } else if (speechResult.includes('더블 불고기 버거') || speechResult.includes('더블불고기버거')) {
            this.speechItem('더블 불고기 버거', count)
          } else if (speechResult.includes('슈슈 버거') || speechResult.includes('슈슈버거')) {
            this.speechItem('슈슈 버거', count)
          } else if (speechResult.includes('더블 빅맥')) {
            this.speechItem('더블 빅맥', count);
          } else if (speechResult.includes('빅맥 BLT')) {
            this.speechItem('빅맥® BLT', count);
          } else if (speechResult.includes('더블 쿼터파운더 치즈버거')) {
            this.speechItem('더블 쿼터파운더® 치즈', count);
          } else if (speechResult.includes('쿼터파운더 치즈버거')) {
            this.speechItem('쿼터파운더® 치즈 세트버거', count);
          } else if (speechResult.includes('맥크리스피 디럭스 버거')) {
            this.speechItem('맥크리스피™ 디럭스 버거 세트', count);
          } else if (speechResult.includes('맥크리스피 클래식 버거')) {
            this.speechItem('맥크리스피™ 클래식 버거', count);
          } else if (speechResult.includes('트리플 치즈 버거')) {
            this.speechItem('트리플 치즈 버거', count);
          } else if (speechResult.includes('맥스파이시 상하이 버거')) {
            this.speechItem('맥스파이시® 상하이 버거', count);
          } else if (speechResult.includes('1955 버거')) {
            this.speechItem('1955® 버거', count);
          } else if (speechResult.includes('맥치킨 모짜렐라 버거')) {
            this.speechItem('맥치킨® 모짜렐라', count);
          } else if (speechResult.includes('맥치킨')) {
            this.speechItem('맥치킨®', count);
          } else if (speechResult.includes('에그 불고기 버거')) {
            this.speechItem('에그 불고기 버거', count);
          } else if (speechResult.includes('불고기 버거') || speechResult.includes('불고기버거')) {
            this.speechItem('불고기 버거', count);
          } else if (speechResult.includes('슈비 버거')) {
            this.speechItem('슈비 버거', count);
          } else if (speechResult.includes('베이컨 토마토 디럭스')) {
            this.speechItem('베이컨 토마토 디럭스', count);
          } else if (speechResult.includes('치즈버거')) {
            this.speechItem('치즈버거', count);
          } else if (speechResult.includes('더블 치즈버거')) {
            this.speechItem('더블 치즈버거', count);
          } else if (speechResult.includes('햄버거')) {
            this.speechItem('햄버거', count);
        } else if (speechResult.includes('춘식이 고구마 후라이') || speechResult.includes('준식이 고구마 후라이')) {
          this.speechItem('춘식이 고구마 후라이', count);
        } else if (speechResult.includes('맥윙 두 조각') || speechResult.includes('맥윙 2조각') || speechResult.includes('맥인 두 조각') || speechResult.includes('맥인 2조각') || speechResult.includes('매직 두조각') || speechResult.includes('매직 두 조각')) {
          this.speechItem('맥윙™ 2조각', count);
        } else if (speechResult.includes('맥윙 내 조각') || speechResult.includes('맥윙 4조각') || speechResult.includes('맥인 내 조각') || speechResult.includes('맥인 4조각') || speechResult.includes('매직 네조각') || speechResult.includes('매직 네 조각') || speechResult.includes('매직 내조각') || speechResult.includes('매직 내 조각')) {
          this.speechItem('맥윙™ 4조각', count);
        } else if (speechResult.includes('"맥윙 여덟 조각"') || speechResult.includes('맥윙 8조각') || speechResult.includes('맥인 여덟 조각') || speechResult.includes('맥인 8조각') || speechResult.includes('매직 여덟조각') || speechResult.includes('매직 여덟 조각')) {
          this.speechItem('맥윙™ 8조각', count);
        } else if (speechResult.includes('슈림프 스낵랩') || speechResult.includes('슈림프스낵랩') || speechResult.includes('쉬림프 스낵랩')) {
          this.speechItem('슈림프 스낵랩', count);
        } else if (speechResult.includes('상하이 치킨 스낵랩') || speechResult.includes('상하이치킨 스낵랩') || speechResult.includes('상하이치킨스낵랩')) {
          this.speechItem('상하이 치킨 스낵랩', count);
        } else if (speechResult.includes('코울슬로')) {
          this.speechItem('코울슬로', count);
        } else if (speechResult.includes('골든 모짜렐라 치즈스틱 네 조각') || speechResult.includes('골든 모짜렐라 치즈 스틱 4조각') || speechResult.includes('골든 모짜렐라 치즈 스틱 내 조각') || speechResult.includes('골든 모짜렐라 치즈스틱 내 조각') || speechResult.includes('골든 모짜렐라 치즈스틱 내조각')) {
          this.speechItem('골든 모짜렐라 치즈스틱 4조각', count);
        } else if (speechResult.includes('골든 모짜렐라 치즈스틱 두 조각') || speechResult.includes('골든 모짜렐라 치즈 스틱 2조각') || speechResult.includes('골든 모짜렐라 치즈 스틱 두 조각') || speechResult.includes('골든 모짜렐라 치즈스틱 두조각')) {
          this.speechItem('골든 모짜렐라 치즈스틱 2조각', count);
        } else if (speechResult.includes('맥너겟 여섯 조각') || speechResult.includes('맥너겟 여섯조각') || speechResult.includes('맥너겟 6조각')) {
          this.speechItem('맥너겟® 6조각', count);
        } else if (speechResult.includes('맥너겟 네 조각') || speechResult.includes('맥너겟 내 조각') || speechResult.includes('맥너겟 내조각') || speechResult.includes('맥너겟 네조각') || speechResult.includes('맥너겟 4조각')) {
          this.speechItem('맥너겟® 4조각', count);
        } else if (speechResult.includes('맥스파이시 치킨텐더 두 조각') || speechResult.includes('맥스파이시 치킨텐더 두조각') || speechResult.includes('맥스파이시 치킨텐더 2조각')) {
          this.speechItem('맥스파이시® 치킨텐더 2조각', count);
        } else if (speechResult.includes('프렌치 프라이') || speechResult.includes('후렌치 후라이') || speechResult.includes('프렌치 후라이') || speechResult.includes('후렌치 프라이')) {
          this.speechItem('후렌치 후라이', count);
        } else if (speechResult.includes('스위트 앤 사워 소스') || speechResult.includes('스위트 소스')) {
          this.speechItem('디핑소스 추가 구매 - 스위트 앤 사워 소스', count);
        } else if (speechResult.includes('케이준 소스') || speechResult.includes('제이준 소스')) {
          this.speechItem('디핑소스 추가 구매 - 케이준 소스', count);
        } else if (speechResult.includes('스위트 칠리 소스') || speechResult.includes('스위트칠리 소스') || speechResult.includes('스위트 칠리소스')) {
          this.speechItem('디핑소스 추가 구매 - 스위트 칠리 소스', count);
          } else if (speechResult.includes('자두 천도복숭아 칠러') || speechResult.includes('천도복숭아 칠러') || speechResult.includes('자두 천도복숭아 7로') || speechResult.includes('천도복숭아 7로')) {
              this.speechItem('자두 천도복숭아 칠러', count);
          } else if (speechResult.includes('제주 한라봉 칠러') || speechResult.includes('한라봉 칠러') || speechResult.includes('제주 한라봉 7로') || speechResult.includes('제주 한라봉 7로')) {
              this.speechItem('제주 한라봉 칠러', count);
          } else if (speechResult.includes('코카콜라')) {
              this.speechItem('코카-콜라', count);
          } else if (speechResult.includes('코카콜라 제로')) {
              this.speechItem('코카-콜라 제로', count);
          } else if (speechResult.includes('스프라이트')) {
              this.speechItem('스프라이트', count);
          } else if (speechResult.includes('환타')) {
              this.speechItem('환타', count);
          } else if (speechResult.includes('디카페인 아이스 카페라떼 시럽없음') || speechResult.includes('디카페인 아이스 카페라떼 시럽 없음')) {
              this.speechItem('디카페인 아이스 카페라떼(시럽없음)', count); 
          } else if (speechResult.includes('디카페인 아이스 아메리카노 시럽없음') || speechResult.includes('디카페인 아이스 아메리카노 시럽 없음')) {
              this.speechItem('디카페인 아이스 아메리카노(시럽없음)', count);
          } else if (speechResult.includes('아이스 아메리카노 시럽 없음')) {
              this.speechItem('아이스 아메리카노(시럽 없음)', count);
          } else if (speechResult.includes('아이스 드립 커피 시럽 없음')) {
              this.speechItem('아이스 드립 커피(시럽 없음)', count);
          } else if (speechResult.includes('디카페인 아이스 바닐라 라떼')) {
              this.speechItem('디카페인 아이스 바닐라 라떼', count);
          } else if (speechResult.includes('디카페인 카페 라떼')) {
              this.speechItem('디카페인 카페 라떼', count);
          } else if (speechResult.includes('디카페인 카푸치노')) {
              this.speechItem('디카페인 카푸치노', count);
          } else if (speechResult.includes('디카페인 아메리카노')) {
              this.speechItem('디카페인 아메리카노', count); 
          } else if (speechResult.includes('아이스 바닐라 라떼') || speechResult.includes('아이스 바닐라라떼')) {
              this.speechItem('아이스 바닐라 라떼', count);
          } else if (speechResult.includes('디카페인 바닐라 라떼') || speechResult.includes('디카페인 바닐라라떼')) {
              this.speechItem('디카페인 바닐라 라떼', count);
          } else if (speechResult.includes('바닐라 라떼') || speechResult.includes('바닐라라떼')) {
              this.speechItem('바닐라 라떼', count);
          } else if (speechResult.includes('카페 라떼') || speechResult.includes('카페라떼')) {
              this.speechItem('카페 라떼', count);
          } else if (speechResult.includes('카푸치노')) {
              this.speechItem('카푸치노', count);    
          } else if (speechResult.includes('아메리카노')) {
              this.speechItem('아메리카노', count);
          } else if (speechResult.includes('드립커피') || speechResult.includes('드립 커피')) {
              this.speechItem('드립커피', count);
          } else if (speechResult.includes('바닐라 쉐이크') || speechResult.includes('바닐라쉐이크')) {
              this.speechItem('바닐라 쉐이크', count);
          } else if (speechResult.includes('딸기 쉐이크') || speechResult.includes('딸기쉐이크')) {
              this.speechItem('딸기 쉐이크', count);
          } else if (speechResult.includes('초코 쉐이크') || speechResult.includes('초코쉐이크')) {
              this.speechItem('초코 쉐이크', count);
          } else if (speechResult.includes('생수')) {
              this.speechItem('생수', count);
        } else if (speechResult.includes('매장')) {
          EventBus.$emit('select-option', '매장')
          this.speak('매장을 골랐습니다');
        } else if (speechResult.includes('포장')) {
          EventBus.$emit('select-option', '포장')
          this.speak('포장을 골랐습니다');
        } else if (speechResult.includes('카드')) {
          EventBus.$emit('select-payment', '카드결제');
          this.speak('카드로 결제하겠습니다');
        } else if (speechResult.includes('쿠폰')) {
          EventBus.$emit('select-payment', '쿠폰결제');
          this.speak('카드로 결제하겠습니다');
        } else if (speechResult.includes('세트') || speechResult.includes('새트')) {
          EventBus.$emit('sendMenuCategory', 'set')
        } else {
          this.speak('다시한번 말해주세요');
        }
      };

      this.recognition.onerror = (event) => {
        console.error('Speech recognition error:', event);
      };

      // 음성 인식이 종료될 때 다시 시작
      this.recognition.onend = () => {
        this.recognition.start();
      };
    },
  }
}
  
</script>