import React, { useState, useMemo } from 'react';

// === 추천 문제집 데이터베이스 ===
const WORKBOOK_DATABASE = {
  // 수학 (Math)
  수학: {
    하: [
      { name: "개념원리 수학", desc: "수학의 기본 개념을 가장 쉽고 체계적으로 잡아주는 대한민국 대표 개념서", hash: ["개념완성", "독학가능", "기초탄탄"] },
      { name: "체크체크 수학", desc: "진도 교재와 개념 드릴로 나누어져 반복 학습과 기초 연산력을 동시에 기르는 교재", hash: ["개념쉬움", "반복학습", "예습용"] },
      { name: "라이트쎈 수학", desc: "수학을 어려워하는 학생들을 위해 난이도를 낮춘 쉬운 유형 학습서", hash: ["기초유형", "기본기강화", "쎈주니어"] },
      { name: "수력충전 수학", desc: "쉬운 문제의 반복 학습을 통해 수학적 연산력을 기르는 연산 전문 교재", hash: ["연산력", "기초반복", "자신감상승"] },
      { name: "디딤돌 개념연산", desc: "이미지로 이해하는 원리와 단계별 연산 문제로 개념을 머릿속에 각인시키는 교재", hash: ["이미지개념", "연산공부", "기초추천"] }
    ],
    중: [
      { name: "쎈 (SSEN) 수학", desc: "모든 수학 문제를 유형별, 난이도별(A, B, C)로 총망라한 최고의 베스트셀러", hash: ["유형마스터", "내신완벽대비", "국민문제집"] },
      { name: "개념원리 RPM", desc: "개념원리와 링크되어 개념 이해와 유형 해결을 동시에 마스터할 수 있는 유형서", hash: ["유형연계", "중간고사대비", "다양한문항"] },
      { name: "우공비 수학", desc: "이미지 연상 학습법과 상세한 해설로 개념과 유형을 친근하게 익히는 교재", hash: ["시각적학습", "자세한해설", "기본유형"] },
      { name: "만렙 PM 수학", desc: "핵심 유형만을 엄선하여 실전 감각을 극대화해주는 실전 대비 문제집", hash: ["실전용", "핵심유형", "시간단축"] },
      { name: "마플시너지 수학 (고등용)", desc: "내신 1등급을 가르는 핵심 유형과 방대한 기출 문항을 담은 종합 문제집", hash: ["고등필수", "엄청난문제량", "기출내신"] }
    ],
    상: [
      { name: "블랙라벨 수학", desc: "상위 1%를 지향하는 명품 수학 교재로, 내신 변별력 문항과 고난도 수능 기출 수록", hash: ["최상위권", "킬러문항", "내신1등급"] },
      { name: "일품 수학", desc: "최고 수준의 핵심 문제와 고난도 서술형 문제를 집중 공략하는 심화 교재", hash: ["상위권도약", "서술형대비", "준킬러공략"] },
      { name: "최상위 수학", desc: "중·고등 수학의 최고 레벨 문제들을 수록하여 수학적 사고력을 극한으로 올리는 교재", hash: ["경시수준", "사고력확장", "최강도전"] },
      { name: "에이급 수학 (중등용)", desc: "상위권 학생들이 거쳐 가는 대표적인 심화 교재로 수학의 깊이를 극대화함", hash: ["중등끝판왕", "상위권필수", "최고난도"] },
      { name: "수학의 신", desc: "단순 계산이 아닌 창의적 발상과 종합 능력을 테스트하는 명품 심화 문제집", hash: ["창의적발상", "고난도심화", "수능킬러"] }
    ]
  },
  // 국어 (Korean)
  국어: {
    하: [
      { name: "체크체크 국어", desc: "교과서 개념을 한눈에 파악하고 가장 쉬운 문제부터 차근차근 다지는 기본서", hash: ["기초국어", "쉬운개념", "교과서밀착"] },
      { name: "예비 매3비", desc: "매일 3개씩 비문학 독해 기본기를 다질 수 있도록 최적화된 입문용 독해책", hash: ["비문학입문", "매일공부", "자기주도"] },
      { name: "빠작 중학 국어 비문학 독해", desc: "인문, 사회, 과학, 예술 등 다양한 지문으로 비문학 독해의 기본 틀을 잡는 책", hash: ["비문학기초", "지문분석", "독해력"] },
      { name: "첫 오감도 국어", desc: "수능 및 내신 국어의 영역별 핵심 출제 포인트를 가볍게 맛볼 수 있는 입문서", hash: ["수능맛보기", "영역별기초", "고등입문"] },
      { name: "EBS 한 장 수능 국어", desc: "하루에 한 장씩 가벼운 분량으로 핵심 어휘와 기초 문법을 익히는 교재", hash: ["하루한장", "핵심어휘", "쉬운학습"] }
    ],
    중: [
      { name: "매3비 (매일 3개 지문 비문학)", desc: "수능 및 모의고사 국어 영역의 고정 1등급을 만드는 전설적인 비문학 기출 문제집", hash: ["수능비문학", "기출분석", "오답노트필수"] },
      { name: "매3문 (매일 3개 지문 문학)", desc: "문학 작품 감상법과 수능 기출을 유기적으로 결합하여 문학 약점을 극복하는 책", hash: ["수능문학", "작품감상", "기출연습"] },
      { name: "윤혜정의 개념의 나비효과 (EBS)", desc: "국어 실력을 수직 상승시키는 기본 문학/독해/언어 개념의 핵심 로드맵", hash: ["EBS추천", "개념완벽", "국어바이블"] },
      { name: "자이스토리 국어 독해 기본", desc: "단계별 문제를 통해 실전 기출 문항의 구조를 스스로 분석하게 만드는 교재", hash: ["기출분석", "해설최고", "실전준비"] },
      { name: "우공비 국어 평가문제집", desc: "전국 주요 중/고교 내신 시험 기출 경향을 정교하게 반영한 내신 적중서", hash: ["내신백점", "단원평가", "빈출유형"] }
    ],
    상: [
      { name: "마더텅 수능국어 기출문제집", desc: "압도적인 두께와 초고화질 해설을 자랑하는 수능 기출의 절대 강자", hash: ["두꺼운해설", "기출끝판왕", "고난도모의고사"] },
      { name: "블랙라벨 국어 독해력", desc: "고난도 수능 및 평가원 비문학 지문을 한층 더 날카롭게 분석하는 최상위서", hash: ["고난도비문학", "추론능력", "1등급전용"] },
      { name: "국어 영역의 비밀 (국비)", desc: "수능 국어의 논리적 사고체계를 근본적으로 교정하고 킬러 지문을 파괴하는 교재", hash: ["논리교정", "구조적분석", "수능킬러"] },
      { name: "해법 문학 세트", desc: "대한민국 모든 교과서 수록 고전/현대 문학 작품들을 심층 분석해주는 문학 백과사전", hash: ["문학올킬", "교과서연계", "작품해설"] },
      { name: "한수 모의고사 국어 봉투팩", desc: "실제 수능 시험장 분위기와 동일한 압박감에서 연습할 수 있는 고품격 실전 모의고사", hash: ["실전모의고사", "시간배분", "초고난도"] }
    ]
  },
  // 영어 (English)
  영어: {
    하: [
      { name: "중학영문법 3800제 스타터", desc: "기초 영문법을 완전 정복하기 위한 첫걸음, 풍부한 쓰기 문제 위주의 워크북", hash: ["기초영문법", "쓰기연습", "초보추천"] },
      { name: "천일문 Starter", desc: "대표 구문을 통해 단어와 기초 해석력을 동시에 훈련하는 구문 독해의 입문서", hash: ["구문독해기초", "직독직해", "명품문장"] },
      { name: "워드마스터 중학 기초", desc: "영어 공부의 절반인 어휘를 완벽히 책임지는 단어 암기 바이블의 입문용", hash: ["어휘기초", "암기카드", "매일테스트"] },
      { name: "그래머존 입문편", desc: "깔끔하고 세련된 시각 자료로 영문법의 기본 지도를 그릴 수 있게 돕는 개념서", hash: ["시각교재", "구조이해", "문법첫걸음"] },
      { name: "능률중학영어 기본", desc: "중학 교과서 어휘와 기본 문장 구조를 학습하기 편리하도록 구성한 전천후 기본서", hash: ["교과서수준", "기초해석", "내신대비"] }
    ],
    중: [
      { name: "중학영문법 3800제 (학년별)", desc: "전국 중학교 시험 기출 문제를 완전히 분석해 완벽한 문법 내신 점수를 주는 최다 판매 영문법서", hash: ["내신문법1위", "엄청난연습량", "서술형대비"] },
      { name: "천일문 기본 (Basic)", desc: "1001개의 엄선된 문장을 통째로 분석해 영어 독해의 눈을 번쩍 뜨이게 하는 교재", hash: ["구문독해", "해석의달인", "인증교재"] },
      { name: "자이스토리 영어 독해 기본", desc: "기출 지문을 유형별로 나누어 정밀하게 끊어 읽는 습관을 정착시키는 리딩 실전서", hash: ["끊어읽기", "독해유형", "기출패턴"] },
      { name: "워드마스터 수능 2000", desc: "고등 영어의 절대적 기준! 수능 및 평가원 빈출 2,000단어를 최적의 주기로 안착시키는 단어장", hash: ["수능단어", "최다판매", "기출단어"] },
      { name: "백발백중 영어 기출문제집", desc: "학교별 교과서 맞춤형 문제와 적중률 99% 예상 문제를 수록해 학교 내신을 완벽 방어하는 교재", hash: ["내신만점", "출판사별교재", "족보수준"] }
    ],
    상: [
      { name: "천일문 완성 (Master)", desc: "수능 최고난도 및 추상적 구문을 완벽히 해부하여 어떠한 긴 문장도 관통하는 눈을 길러주는 교재", hash: ["고난도구문", "수능1등급", "번역기탈출"] },
      { name: "자이스토리 영어 독해 실전/고난도", desc: "고난도 장문 독해, 빈칸 추론, 순서 배열 등 오답률 상위 킬러 문항을 정밀 조준하는 교재", hash: ["빈칸추론", "수능킬러", "시간단축팁"] },
      { name: "EBS 수능특강 영어", desc: "대한민국 고3 필독서이자 수능 연계 체감을 경험하게 해 주는 최고의 학업 표준", hash: ["EBS연계", "고3필수", "학평대비"] },
      { name: "마더텅 수능기출 영어 독해", desc: "실제 수능 시험 시험지와 동일한 연도별 배치를 통해 기출의 흐름을 뼛속까지 익히는 문제집", hash: ["기출연도별", "모의고사대비", "완벽해설"] },
      { name: "블랙라벨 영어 내신/수능 독해", desc: "변별력 높은 초고난도 지문을 기반으로 영리한 논리 독해력을 요구하는 프리미엄 심화 교재", hash: ["최상위논리", "1등급안착", "프리미엄독해"] }
    ]
  },
  // 과학 (Science)
  과학: {
    하: [
      { name: "체크체크 과학", desc: "단원별 핵심 원리를 시각화된 설명과 쉬운 단계별 예제로 가볍게 정복하는 입문서", hash: ["핵심요약", "쉬운원리", "중학과학"] },
      { name: "우공비 과학", desc: "그림과 친절한 풀이로 어려운 과학 개념도 만화처럼 이해할 수 있도록 디자인된 개념 문제집", hash: ["이미지트레이닝", "기초과학", "쉽게이해"] },
      { name: "개념 완자 과학", desc: "마치 친절한 과외 선생님이 옆에서 가르쳐 주는 듯한 친밀하고 상세한 구성의 개념 교과서", hash: ["과외느낌", "기초탄탄", "이론위주"] },
      { name: "EBS 꿀선택 과학", desc: "꼭 알아야 할 단 핵심 내용만을 빠르게 훑어 실력에 탄력을 더해주는 초심자용 실력 다지기용", hash: ["핵심압축", "가성비최고", "인강무료"] },
      { name: "한끝 과학 기본", desc: "군더더기를 완전히 뺀 교과서 핵심 체계도를 통해 가장 영양가 높은 개념 공부를 선사하는 교재", hash: ["핵심체계", "간결함", "개념위주"] }
    ],
    중: [
      { name: "오투 과학", desc: "풍부한 시각 자료와 기출 유형 분석을 탑재하여 대한민국 중·고교생 80%가 거치는 과학 베스트셀러", hash: ["과학1위", "기출다량", "내신마스터"] },
      { name: "완자 과학", desc: "두꺼운 부피만큼이나 빈틈없이 철저한 독학용 자습서 겸 고품격 평가 교재", hash: ["독학최적화", "풍부한이론", "전과목커버"] },
      { name: "자이스토리 과학", desc: "개념 정리와 내신·수능 모의고사 기출 분석까지 한 번에 완벽하게 해결해 주는 만능 교재", hash: ["수능기출", "정밀분석", "문제집추천"] },
      { name: "우공비 Q+Q 과학", desc: "개념과 필수 기출문제를 1:1로 매칭시켜 복습의 효과를 극한으로 끌어올린 실전 지향형 교재", hash: ["일대일매칭", "피드백학습", "문제풀이형"] },
      { name: "EBS 뉴탐스런 과학", desc: "풍성하고 유려한 개념 설명과 학교 시험에 빠지지 않는 단골 문제들을 집중 수록한 대표 학습서", hash: ["EBS명작", "인터넷강의연계", "검증된교재"] }
    ],
    상: [
      { name: "하이탑 (HIGH TOP) 과학", desc: "단순 고교 교육과정을 뛰어넘어 대학 기초 교양 수준의 깊이까지 통찰하게 하는 자연과학의 전설", hash: ["영재학교준비", "심화끝판왕", "학술적깊이"] },
      { name: "일등급 만들기 과학", desc: "시험 변별력을 기르기 위한 까다로운 신유형 및 고난도 서답형 문제를 한데 모은 일등 공신 교재", hash: ["내신1등급", "고난도문제", "상위권선택"] },
      { name: "블랙라벨 과학", desc: "이공계 인재들의 수학적, 논리적 과학 추론 능력을 고난도 킬러 문제를 통해 자극하는 최고급 고난도서", hash: ["킬러문항", "수행평가대비", "초정밀추론"] },
      { name: "완자 기출픽 과학", desc: "전국 자사고 및 특목고에서 실제로 출제된 최우수 과학 명품 기출문제만을 핀셋으로 집어낸 문제집", hash: ["자사고기출", "명품문제", "상위권검증"] },
      { name: "올리드 과학 심화", desc: "개념의 빈틈을 철저히 막고, 시험의 당락을 가르는 응용 영역을 독창적인 방식으로 풀어나가는 교재", hash: ["응용문제집", "서술형공략", "개념응용"] }
    ]
  },
  // 사회 (Social Studies)
  사회: {
    하: [
      { name: "체크체크 사회", desc: "사회 과목의 복잡한 지도와 도표, 생소한 어휘들을 도식화하여 기초 지식을 다져주는 입문서", hash: ["용어사전탑재", "시각도표", "기초탄탄"] },
      { name: "우공비 사회", desc: "다양한 사진 자료와 유기적인 마인드맵식 요약으로 지루한 사회 과목을 즐겁게 학습하는 교재", hash: ["마인드맵", "사진자료", "암기기초"] },
      { name: "한끝 사회 (기본)", desc: "개념의 뼈대를 깔끔하게 세워주어 중하위권 학생들이 학습 흐름을 찾도록 이끄는 친절한 길잡이", hash: ["뼈대세우기", "체계적정리", "친절한교재"] },
      { name: "EBS 한 장 사회", desc: "어려운 법률, 경제, 사회 구조를 만화 일러스트와 핵심 퀴즈로 가볍게 다지는 초보자 전용 교재", hash: ["만화해설", "초보추천", "하루오분"] },
      { name: "셀파 사회", desc: "선생님들의 비밀 노트와 같은 핵심 비법이 수록되어 사회 지식이 마법처럼 저절로 외워지는 기본서", hash: ["비법노트", "비밀암기법", "시험직전효과"] }
    ],
    중: [
      { name: "한끝 사회", desc: "교과서의 핵심을 날카롭게 찌르고 출제 예상 모의고사로 내신 고득점을 든든하게 보장하는 국민 교재", hash: ["내신보증수표", "출제율100", "정리대왕"] },
      { name: "완자 사회", desc: "전체 출판사 교과 내용을 통틀어 단 하나의 구멍도 없도록 완벽 정리한 백과사전식 통합 학습서", hash: ["철저한분석", "자기주도학습", "백과사전식"] },
      { name: "자이스토리 사회", desc: "모의고사 및 수능 기출 자료를 철저히 분류하여 자료 해석형 문제의 힘을 극대화시키는 문제집", hash: ["자료해석", "기출트렌드", "고득점전략"] },
      { name: "EBS 뉴탐스런 사회", desc: "현직 스타 강사들의 명품 강의 노하우가 지면에 그대로 녹아 있어 흐름을 파악하기 가장 좋은 교재", hash: ["EBS스타강사", "명품인강", "구조적학습"] },
      { name: "올리드 사회", desc: "내신 성적 향상을 위해 필수 개념 암기와 시험에 자주 나오는 대표 유형 훈련을 압축한 고효율 교재", hash: ["고효율학습", "핵심유형", "내신대비"] }
    ],
    상: [
      { name: "1등급 만들기 사회", desc: "까다로운 수능형 3점 문항과 변별력 높은 서술형 문항을 철저하게 대비시키는 상위권 맞춤 전문서", hash: ["상위권인증", "고급자료분석", "수능완벽연계"] },
      { name: "마더텅 사회탐구", desc: "방대한 역대 기출 자료와 정교한 입체적 해설을 제공하여 수능 사회탐구 만점을 완성해 주는 바이블", hash: ["기출바이블", "해설명가", "모의고사완벽"] },
      { name: "블랙라벨 사회", desc: "수능 킬러 문항과 변별력 높은 고난도 추론 지문만을 체계적으로 엄선해 상위권 독해·분석력을 키우는 교재", hash: ["킬러문항격파", "변별력대비", "최상의분석력"] },
      { name: "완자 기출픽 사회탐구", desc: "기출 흐름의 모든 것을 담아 복잡한 사회 현상을 명쾌하게 풀어낸 수능 내비게이터 교재", hash: ["기출픽", "명쾌한정리", "만점자선정"] },
      { name: "특등급 사회탐구", desc: "최근 5개년 오답률 상위 최고난도 문항과 지엽적 개념까지 남김없이 털어내 만점으로 향하는 계단을 제공하는 책", hash: ["오답타파", "지엽적개념", "만점도약"] }
    ]
  },
  // 역사 (History)
  역사: {
    하: [
      { name: "한끝 역사", desc: "복잡하게 얽힌 역사의 인과관계와 시간의 흐름을 보기 쉽게 타임라인으로 정리한 기본 암기서", hash: ["시간여행", "타임라인식", "스토리텔링"] },
      { name: "우공비 역사", desc: "역사 속 주요 유물과 유적지 사진 자료가 생생하게 담겨 있어 눈으로 먼저 익히는 친근한 입문서", hash: ["생생사진", "유물박물관", "쉬운암기"] },
      { name: "EBS 수능 7일 완성 역사", desc: "딱 7일 만에 전 범위의 역사 흐름과 필수 유적, 인물을 압축 정리할 수 있는 슬림형 요약집", hash: ["7일완성", "초스피드요약", "시간단축교재"] },
      { name: "용선생 만화 한국사", desc: "방대한 역사적 지식을 재미있고 유쾌한 스토리텔링 만화로 자연스럽게 습득하게 만드는 입문 필독서", hash: ["만화한국사", "스토리텔링", "베스트셀러"] },
      { name: "체크체크 역사", desc: "시험 문제 출제 비중이 매우 높은 사료와 인물 탐구를 깔끔하게 분류하여 기초를 잡아주는 필수 교재", hash: ["사료정리", "인물탐구", "내신기초"] }
    ],
    중: [
      { name: "완자 역사", desc: "역사 교과서의 세세한 문장 하나까지 꼼꼼하게 풀어서 해석해 주는 완벽한 독학 자습형 문제집", hash: ["친절한자습서", "문장분석", "역사마스터"] },
      { name: "올리드 역사", desc: "단원별 알짜 개념과 필수 문제를 결합하여 시험 보기 전 지식을 빠르게 단기 완성시켜 주는 완성형 문제집", hash: ["알짜정리", "단기속성", "내신고득점"] },
      { name: "자이스토리 역사 (한국사/동아시아사)", desc: "기출 지문과 역사 지도를 입체적으로 매칭하여 수능 및 모의고사의 출제 원리를 꿰뚫어 보게 하는 교재", hash: ["역사지도마스터", "기출풀이", "수능대비"] },
      { name: "EBS 뉴탐스런 역사", desc: "전문 연구원과 스타 강사진이 기획하여 역사의 큰 흐름과 뼈대를 가장 완성도 높게 전달하는 정석 학습서", hash: ["흐름잡기", "검증된교안", "정석역사"] },
      { name: "메가스터디 역사 기출문제집", desc: "내신 시험의 적중 문항과 지엽적 사료들까지 완벽 대응하도록 최적화된 고효율 실전 평가 교재", hash: ["실전문제다량", "기출기반", "메가추천"] }
    ],
    상: [
      { name: "1등급 만들기 역사 (한국사/세계사)", desc: "각종 시험에서 변별력을 위해 출제되는 고난도 지엽적 문제와 3점 킬러 문제를 철저하게 완벽 방어하는 교재", hash: ["지엽킬러", "자사고내신", "1등급수호"] },
      { name: "마더텅 역사 기출문제집", desc: "방대한 기출 사료 및 연표를 완전히 조각내어 분석하고 상세한 입체 오답 해설로 만점을 공고히 하는 교재", hash: ["연표분석", "입체해설", "사료해석완성"] },
      { name: "블랙라벨 역사", desc: "심화 한국사 및 역사 과목의 고난도 사료 해석, 연표 추론 등 최고 등급 완성을 가로막는 장벽들을 허무는 심화서", hash: ["사료추론", "연표마스터", "최고난도"] },
      { name: "자이스토리 고난도 역사", desc: "최고 오답률을 자랑하는 복잡한 시대 연표 퍼즐 문항들과 희귀 사료 분석에 특화된 상위 1% 전문 학습서", hash: ["연표퍼즐", "희귀사료", "상위일프로"] },
      { name: "수능 1등급 완성 봉투모의고사 역사", desc: "수능 및 평가원 시험 기출 트렌드를 반영해 실제 고사장 압박감 속에서 실전 훈련을 가능하게 해주는 수능 최종 병기", hash: ["봉투모의고사", "만점종지부", "실전압박"] }
    ]
  }
};

export default function App() {
  // === 상태 관리 ===
  const [grade, setGrade] = useState(''); // 중1, 중2, 중3, 고1, 고2, 고3
  const [subject, setSubject] = useState(''); // 국어, 영어, 수학, 과학, 사회, 역사
  const [difficulty, setDifficulty] = useState(''); // 상, 중, 하
  const [step, setStep] = useState(1); // 1: 학년 선택, 2: 과목 선택, 3: 난이도 선택, 4: 추천 결과
  const [bookmarks, setBookmarks] = useState([]); // 북마크 리스트

  // === UI 아이콘 데이터 ===
  const subjectIcons = {
    국어: "📖",
    영어: "🔤",
    수학: "📐",
    과학: "🔬",
    사회: "🌍",
    역사: "👑"
  };

  const difficultyColors = {
    하: { bg: "bg-emerald-50", text: "text-emerald-700", border: "border-emerald-200", badge: "bg-emerald-500", label: "기초 탄탄 / 개념 중심" },
    중: { bg: "bg-blue-50", text: "text-blue-700", border: "border-blue-200", badge: "bg-blue-500", label: "실전 대비 / 기출 응용" },
    상: { bg: "bg-rose-50", text: "text-rose-700", border: "border-rose-200", badge: "bg-rose-500", label: "최상위권 / 최고 심화" }
  };

  // === 추천 비즈니스 로직 ===
  const recommendations = useMemo(() => {
    if (!subject || !difficulty) return [];
    
    // 데이터베이스 조회
    const rawList = WORKBOOK_DATABASE[subject]?.[difficulty] || [];
    
    // 선택한 학년 정보를 기반으로 상품 이름 등에 학년 접미사 동적 결합
    return rawList.map((item, index) => {
      // YES24 검색 최적화 링크 자동 생성
      const searchKeyword = `${grade} ${item.name}`;
      const buyLink = `https://www.yes24.com/Product/Search?query=${encodeURIComponent(searchKeyword)}`;
      
      return {
        id: `${grade}-${subject}-${difficulty}-${index}`,
        name: `${grade} ${item.name}`,
        desc: item.desc,
        hash: item.hash,
        buyLink
      };
    });
  }, [grade, subject, difficulty]);

  // === 북마크 토글 ===
  const toggleBookmark = (book) => {
    if (bookmarks.some(b => b.id === book.id)) {
      setBookmarks(bookmarks.filter(b => b.id !== book.id));
    } else {
      setBookmarks([...bookmarks, book]);
    }
  };

  // === 리셋 / 처음으로 ===
  const handleReset = () => {
    setGrade('');
    setSubject('');
    setDifficulty('');
    setStep(1);
  };

  return (
    <div className="min-h-screen bg-slate-50 font-sans text-slate-800">
      {/* GNB (헤더) */}
      <header className="sticky top-0 z-50 border-b border-slate-200 bg-white/90 backdrop-blur-md">
        <div className="mx-auto flex max-w-6xl items-center justify-between px-4 py-4 md:px-8">
          <div className="flex items-center space-x-3 cursor-pointer" onClick={handleReset}>
            <div className="flex h-10 w-10 items-center justify-center rounded-xl bg-gradient-to-tr from-indigo-600 to-violet-500 text-white shadow-md shadow-indigo-200">
              <span className="text-xl font-bold">📚</span>
            </div>
            <div>
              <h1 className="text-xl font-extrabold tracking-tight bg-gradient-to-r from-indigo-600 to-violet-600 bg-clip-text text-transparent">BookMatch</h1>
              <p className="text-xs text-slate-400 font-medium">초정밀 맞춤형 문제집 추천 서비스</p>
            </div>
          </div>

          {/* 북마크 위젯 */}
          <div className="relative group">
            <button className="flex items-center space-x-2 rounded-lg border border-slate-200 bg-white px-3.5 py-1.5 text-sm font-semibold text-slate-600 shadow-sm transition hover:bg-slate-50 hover:text-indigo-600">
              <span>⭐️ 북마크</span>
              <span className="flex h-5 w-5 items-center justify-center rounded-full bg-indigo-100 text-xs font-bold text-indigo-600">
                {bookmarks.length}
              </span>
            </button>
            
            {/* 북마크 드롭다운 마우스 오버 시 노출 */}
            <div className="absolute right-0 mt-2 w-80 scale-95 opacity-0 pointer-events-none origin-top-right rounded-xl border border-slate-200 bg-white p-4 shadow-xl transition-all duration-200 group-hover:scale-100 group-hover:opacity-100 group-hover:pointer-events-auto z-50">
              <h3 className="border-b border-slate-100 pb-2 text-sm font-bold text-slate-700">📌 마이 북마크 리스트 ({bookmarks.length})</h3>
              <div className="mt-3 max-h-60 overflow-y-auto space-y-2">
                {bookmarks.length === 0 ? (
                  <p className="py-6 text-center text-xs text-slate-400">자주 보고 싶은 문제집을<br/>마크해보세요!</p>
                ) : (
                  bookmarks.map((book) => (
                    <div key={book.id} className="flex items-start justify-between rounded-lg bg-slate-50 p-2.5 transition hover:bg-slate-100">
                      <div className="mr-2 overflow-hidden">
                        <p className="truncate text-xs font-bold text-slate-800">{book.name}</p>
                        <a 
                          href={book.buyLink} 
                          target="_blank" 
                          rel="noopener noreferrer" 
                          className="mt-0.5 inline-block text-[10px] font-bold text-indigo-500 hover:underline"
                        >
                          YES24 사러가기 →
                        </a>
                      </div>
                      <button 
                        onClick={() => toggleBookmark(book)} 
                        className="text-slate-400 hover:text-rose-500 text-xs"
                      >
                        ✕
                      </button>
                    </div>
                  ))
                )}
              </div>
            </div>
          </div>
        </div>
      </header>

      {/* 메인 대시보드 구조 */}
      <main className="mx-auto max-w-4xl px-4 py-8 md:py-16">
        
        {/* 서비스 캐치프레이즈 (진행 중일 때만 상단 노출) */}
        {step < 4 && (
          <div className="mb-10 text-center">
            <span className="inline-block rounded-full bg-indigo-50 px-3 py-1 text-xs font-bold text-indigo-600">
              Book Recommendation Wizard
            </span>
            <h2 className="mt-3 text-3xl font-extrabold tracking-tight text-slate-900 md:text-4xl">
              어떤 문제집을 찾고 계신가요?
            </h2>
            <p className="mt-2 text-slate-500 text-sm md:text-base">
              단 3초 만에 학업 수준과 교과 과정에 딱 맞는 상위권 필독 도서를 매칭해 드립니다.
            </p>
          </div>
        )}

        {/* 진행 현황 인디케이터 바 */}
        {step < 4 && (
          <div className="mb-8 flex items-center justify-center space-x-2">
            {[1, 2, 3].map((s) => (
              <React.Fragment key={s}>
                <div className="flex items-center space-x-1.5">
                  <div className={`flex h-8 w-8 items-center justify-center rounded-full text-xs font-bold transition-all duration-300 ${
                    step === s 
                      ? 'bg-indigo-600 text-white ring-4 ring-indigo-100 scale-110' 
                      : step > s 
                        ? 'bg-indigo-100 text-indigo-600' 
                        : 'bg-slate-200 text-slate-400'
                  }`}>
                    {step > s ? '✓' : s}
                  </div>
                  <span className={`hidden text-xs font-bold sm:inline ${
                    step === s ? 'text-indigo-600' : 'text-slate-400'
                  }`}>
                    {s === 1 ? '학년 선택' : s === 2 ? '과목 선택' : '난이도 선택'}
                  </span>
                </div>
                {s < 3 && <div className={`h-0.5 w-12 rounded transition-colors duration-300 ${step > s ? 'bg-indigo-400' : 'bg-slate-200'}`} />}
              </React.Fragment>
            ))}
          </div>
        )}

        {/* 인터랙티브 마법사 보드 카드 */}
        <div className="overflow-hidden rounded-2xl border border-slate-200 bg-white shadow-xl shadow-slate-100 transition-all">
          
          {/* STEP 1: 학년 선택 */}
          {step === 1 && (
            <div className="p-6 md:p-10">
              <h3 className="text-lg font-bold text-slate-700">Step 1. 학년을 선택해 주세요</h3>
              <p className="text-xs text-slate-400 mb-6">현재 재학 중이거나 선행 학습을 원하는 단계를 클릭해 주세요.</p>
              
              <div className="grid gap-6 md:grid-cols-2">
                {/* 중학교 그룹 */}
                <div className="rounded-xl border border-slate-100 bg-slate-50/50 p-5">
                  <h4 className="mb-4 text-xs font-extrabold tracking-wider text-slate-400 uppercase">🏫 중등 교과정</h4>
                  <div className="grid grid-cols-3 gap-3">
                    {['중1', '중2', '중3'].map((g) => (
                      <button
                        key={g}
                        onClick={() => { setGrade(g); setStep(2); }}
                        className={`flex h-20 flex-col items-center justify-center rounded-xl border bg-white p-3 font-bold transition duration-200 active:scale-95 ${
                          grade === g 
                            ? 'border-indigo-600 text-indigo-600 ring-2 ring-indigo-100' 
                            : 'border-slate-200 text-slate-600 hover:border-slate-300 hover:bg-slate-50'
                        }`}
                      >
                        <span className="text-xl">🎒</span>
                        <span className="mt-1.5 text-sm">{g}</span>
                      </button>
                    ))}
                  </div>
                </div>

                {/* 고등학교 그룹 */}
                <div className="rounded-xl border border-slate-100 bg-slate-50/50 p-5">
                  <h4 className="mb-4 text-xs font-extrabold tracking-wider text-slate-400 uppercase">🏢 고등 교과정</h4>
                  <div className="grid grid-cols-3 gap-3">
                    {['고1', '고2', '고3'].map((g) => (
                      <button
                        key={g}
                        onClick={() => { setGrade(g); setStep(2); }}
                        className={`flex h-20 flex-col items-center justify-center rounded-xl border bg-white p-3 font-bold transition duration-200 active:scale-95 ${
                          grade === g 
                            ? 'border-indigo-600 text-indigo-600 ring-2 ring-indigo-100' 
                            : 'border-slate-200 text-slate-600 hover:border-slate-300 hover:bg-slate-50'
                        }`}
                      >
                        <span className="text-xl">🎓</span>
                        <span className="mt-1.5 text-sm">{g}</span>
                      </button>
                    ))}
                  </div>
                </div>
              </div>
            </div>
          )}

          {/* STEP 2: 과목 선택 */}
          {step === 2 && (
            <div className="p-6 md:p-10">
              <div className="mb-6 flex items-center justify-between">
                <div>
                  <h3 className="text-lg font-bold text-slate-700">Step 2. 공부할 과목을 선택하세요</h3>
                  <p className="text-xs text-slate-400">어떤 영역의 우수 도서 목록을 알아볼까요?</p>
                </div>
                <button 
                  onClick={() => setStep(1)} 
                  className="text-xs font-bold text-slate-400 hover:text-indigo-600 transition"
                >
                  ← 학년 변경
                </button>
              </div>

              <div className="grid grid-cols-2 gap-4 sm:grid-cols-3">
                {Object.keys(WORKBOOK_DATABASE).map((sub) => (
                  <button
                    key={sub}
                    onClick={() => { setSubject(sub); setStep(3); }}
                    className={`group flex flex-col items-center justify-center rounded-xl border p-5 transition duration-200 active:scale-95 ${
                      subject === sub
                        ? 'border-indigo-600 bg-indigo-50/30 text-indigo-600 ring-2 ring-indigo-100'
                        : 'border-slate-200 bg-white text-slate-600 hover:border-slate-300 hover:bg-slate-50'
                    }`}
                  >
                    <span className="text-3xl transition-transform duration-200 group-hover:scale-110">
                      {subjectIcons[sub]}
                    </span>
                    <span className="mt-2 text-sm font-extrabold">{sub}</span>
                  </button>
                ))}
              </div>
            </div>
          )}

          {/* STEP 3: 난이도 선택 */}
          {step === 3 && (
            <div className="p-6 md:p-10">
              <div className="mb-6 flex items-center justify-between">
                <div>
                  <h3 className="text-lg font-bold text-slate-700">Step 3. 학습 난이도를 설정하세요</h3>
                  <p className="text-xs text-slate-400">현재 학생의 실력과 목표 수준에 가장 가까운 기준을 클릭해 주세요.</p>
                </div>
                <button 
                  onClick={() => setStep(2)} 
                  className="text-xs font-bold text-slate-400 hover:text-indigo-600 transition"
                >
                  ← 과목 변경
                </button>
              </div>

              <div className="grid gap-4 md:grid-cols-3">
                {['하', '중', '상'].map((level) => {
                  const design = difficultyColors[level];
                  return (
                    <button
                      key={level}
                      onClick={() => { setDifficulty(level); setStep(4); }}
                      className={`relative overflow-hidden rounded-xl border p-6 text-left transition-all duration-200 active:scale-95 ${
                        difficulty === level
                          ? 'border-indigo-600 ring-2 ring-indigo-100 shadow-md'
                          : 'border-slate-200 bg-white hover:border-slate-300 hover:bg-slate-50'
                      }`}
                    >
                      <div className="flex items-center justify-between">
                        <span className={`inline-block rounded-md px-2.5 py-1 text-xs font-extrabold text-white ${design.badge}`}>
                          난이도 [{level}]
                        </span>
                      </div>
                      <p className="mt-4 text-base font-black text-slate-800">{design.label}</p>
                      <p className="mt-1.5 text-xs leading-relaxed text-slate-400">
                        {level === '하' && '수업 이해를 목표로 기초 이론과 기본 연산 위주로 구성된 입문자용'}
                        {level === '중' && '학교 내신 고득점 및 다양한 실전 문제 풀이에 집중하는 범용 학습자용'}
                        {level === '상' && '상위 1% 특목고/자사고 내신 및 수능 1등급 킬러 문항을 대비하는 고난도용'}
                      </p>
                    </button>
                  );
                })}
              </div>
            </div>
          )}

          {/* STEP 4: 추천 결과 화면 */}
          {step === 4 && (
            <div className="p-6 md:p-10">
              {/* 상단 타겟 요약 및 리셋 버튼 */}
              <div className="mb-8 flex flex-col items-center justify-between border-b border-slate-100 pb-6 md:flex-row">
                <div className="mb-4 text-center md:mb-0 md:text-left">
                  <span className="rounded-full bg-indigo-50 px-3 py-1 text-xs font-bold text-indigo-600">
                    🏆 Matching Success
                  </span>
                  <h3 className="mt-2 text-2xl font-black tracking-tight text-slate-800">
                    {grade} <span className="text-indigo-600">{subject}</span> [{difficulty}] 맞춤 추천 도서
                  </h3>
                  <div className="mt-1.5 flex flex-wrap justify-center gap-1.5 text-xs text-slate-500 md:justify-start">
                    <span className="rounded bg-slate-100 px-2 py-0.5">{grade} 학사일정</span>
                    <span className="rounded bg-slate-100 px-2 py-0.5">{subject} 기출 완벽</span>
                    <span className="rounded bg-slate-100 px-2 py-0.5">최적의 5대 학습 파트너</span>
                  </div>
                </div>
                
                <button
                  onClick={handleReset}
                  className="rounded-xl bg-indigo-600 px-5 py-3 text-sm font-extrabold text-white shadow-md shadow-indigo-100 transition hover:bg-indigo-700 active:scale-95"
                >
                  🔄 다른 조건 추천받기
                </button>
              </div>

              {/* 5종 도서 리스트 */}
              <div className="space-y-6">
                {recommendations.map((book, index) => {
                  const isBookmarked = bookmarks.some(b => b.id === book.id);
                  return (
                    <div 
                      key={book.id} 
                      className="group relative flex flex-col justify-between rounded-xl border border-slate-100 bg-white p-5 shadow-sm transition-all duration-200 hover:-translate-y-1 hover:border-indigo-100 hover:shadow-md md:flex-row md:items-center"
                    >
                      {/* 순위 마크 */}
                      <div className="absolute -left-2.5 -top-2.5 flex h-7 w-7 items-center justify-center rounded-lg bg-gradient-to-tr from-indigo-600 to-indigo-500 text-xs font-black text-white shadow-md">
                        {index + 1}
                      </div>

                      {/* 도서 디테일 */}
                      <div className="md:w-3/4 pr-4">
                        <div className="flex items-center space-x-2">
                          <h4 className="text-lg font-black text-slate-800 group-hover:text-indigo-600 transition">
                            {book.name}
                          </h4>
                          <span className={`rounded px-1.5 py-0.5 text-[10px] font-extrabold uppercase ${difficultyColors[difficulty].bg} ${difficultyColors[difficulty].text}`}>
                            Best {index + 1}
                          </span>
                        </div>
                        <p className="mt-1.5 text-sm leading-relaxed text-slate-500 font-medium">
                          {book.desc}
                        </p>
                        
                        {/* 해시태그 */}
                        <div className="mt-3.5 flex flex-wrap gap-1.5">
                          {book.hash.map((tag, tIdx) => (
                            <span key={tIdx} className="rounded-full bg-slate-50 border border-slate-100 px-2.5 py-0.5 text-xs text-slate-400 font-bold">
                              #{tag}
                            </span>
                          ))}
                        </div>
                      </div>

                      {/* 액션 버튼 */}
                      <div className="mt-5 flex items-center space-x-2 border-t border-slate-50 pt-4 md:mt-0 md:border-0 md:pt-0">
                        {/* 북마크 아이콘 */}
                        <button
                          onClick={() => toggleBookmark(book)}
                          className={`flex h-11 w-11 items-center justify-center rounded-xl border transition ${
                            isBookmarked 
                              ? 'border-amber-300 bg-amber-50 text-amber-500 hover:bg-amber-100' 
                              : 'border-slate-200 bg-white text-slate-400 hover:bg-slate-50 hover:text-slate-600'
                          }`}
                          title="나중을 위해 저장"
                        >
                          ★
                        </button>

                        {/* 구매 링크 */}
                        <a
                          href={book.buyLink}
                          target="_blank"
                          rel="noopener noreferrer"
                          className="flex flex-1 items-center justify-center space-x-1.5 rounded-xl bg-slate-950 px-5 h-11 text-center text-sm font-black text-white shadow-sm transition duration-150 hover:bg-slate-800 md:flex-initial"
                        >
                          <span>구매 링크 (YES24)</span>
                          <span className="text-xs">↗</span>
                        </a>
                      </div>
                    </div>
                  );
                })}
              </div>

              {/* 하단에 추가된 대형 '처음으로 돌아가기' 버튼 */}
              <div className="mt-12 flex flex-col items-center justify-center space-y-3 border-t border-slate-100 pt-10">
                <p className="text-sm font-bold text-slate-400">원하는 문제집을 모두 찾으셨나요?</p>
                <button
                  onClick={handleReset}
                  className="group flex items-center space-x-2 rounded-2xl bg-gradient-to-tr from-indigo-600 to-violet-600 px-10 py-5 text-base font-extrabold text-white shadow-xl shadow-indigo-100 transition duration-200 hover:-translate-y-0.5 hover:shadow-indigo-200 hover:from-indigo-700 hover:to-violet-700 active:scale-95"
                >
                  <span>🏠 처음으로 돌아가기</span>
                </button>
              </div>
            </div>
          )}
        </div>

        {/* 하단 유의 사항 푸터 */}
        <div className="mt-8 text-center text-xs text-slate-400 leading-relaxed font-medium">
          <p>※ 추천 문제집의 구매 링크는 국내 도서 유통 1위 온라인 서점인 YES24의 검색 결과 페이지와 자동으로 연동되어 안전하고 빠른 실시간 구매를 지원합니다.</p>
          <p className="mt-1">※ 본 서비스는 학생의 일반적 학업 성향을 통계적으로 매칭한 결과이며, 학교별 출판사(자습서 및 평가문제집)를 병행하여 학습하면 학습 효율이 극대화됩니다.</p>
        </div>
      </main>
    </div>
  );
}
