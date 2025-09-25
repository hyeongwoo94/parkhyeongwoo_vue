# parkhyeongwoo_vue
1. scss 규칙
 - 공통 컴포넌트는 _common에서 작성. -> index.scss에서 import 중
 - 각 vue / 카테고리별은 새로 scss 생성후 각 컴포넌트에서 import => 각vue에서 import된 scss는 항상 맨상단에 
    @use '../_common' as var;를 작성해서 변수값을 사용해줘야 한다.

2. 설정
  - shims-vue.d를 사용해서 @을 src로 설정해뒀다.