@TemporalType(TemporalType.TIMPSTAMP) -> 최근에는 사용 x 요즘은 그냥 LocalDateTime 사용하면 됨
@Lob 
@Transient : DB와 매핑하지 않겠다


EnumType.ORDINAL : enum 순서를 db에 저장
-> 굉장이 위험함. 사용을 피해야함. enum이 추가되었을 때 순서가 조금이라도 달라지면 중복이 일어날 수 있음. 데이터의 무결성 깨짐
EnumType.STRING : enum에 문자 그대로 db에 저장 

Lob은 필드타입이 문자면 CLOB 그 외 BLOB