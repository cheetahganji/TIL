<<Redis 명령어 정리>> 
ref
  https://redis.io/commands#
  https://redis.io/topics/data-types-intro


SET server:name “circlelcric”
SET ten 10

GET server:name => “circlelcric”


INCR ten => 11		# 1 증가
INCR ten 1 => 12		# 1 증가
INCR ten 10 => 22		# 10 증가

DEL ten		# Key&Value를 삭제
INCR ten => 1


SET resource:lock “Redis Demo”
EXPIRE resource:lock 120		# 120초 후 삭제

TTL resource:lock => 107		# EXPIRE 설정 13초가 지난 후
TTL resource:lock => -2		# 120초 후(-2는 더이상 해당 key가 존재하지 않는단 의미)
TTL resource:lock => -1		# EXPIRE 설정되지 않는 경우에는 -1 반환



*숫자, 문자열 외의 데이터 구조*
1. list
  명령어 : RPUSH, LPUSH, LLEN, LRANGE, LPOP, RPOP
  RPUSH friends “Alice” => 1		# 리스트 마지막에 추가(원소 개수 반환)
  LPUSH friends “Bob” => 2		# 리스트 처음에 추가(원소 개수 반환)

  LRANGE friends 0 -1		# 리스트 원소 전체 반환
  LRANGE friends 0 1  => 1) “Alice”, 2) “Bob”		# 리스트 인덱스 0~1의 원소 반환

  LLEN friends => 2			# 리스트의 현재 길이 반환

  LPOP friends => “Alice”		# 첫 번째 원소 삭제 및 반환
  RPOP friends => “Bob”		# 마지막 원소 삭제 및 반환



2. set : 순서가 없는 list라고 보면되며, 중복된 원소가 없다.
  명령어 : SADD, SREM, SISMEMBER, SMEMBERS, SUNION
  SADD superpowers “flight”		# 원소 추가
  SADD superpowers “x-ray vision”
  SADD superpowers “reflexes”

  SREM superpowers “reflexes”		# 해당 원소 삭제

  SISMEMBER superpowers “flight” => 1	# 해당 원소가 존재여부 확인(존재:1, 부재:0)
  SMEMBERS superpowers 			# 해당 set의 전체 원소 반환

  SADD birdpowers “pecking”
  SADD birdpowers “flight”
  SUNION superpowers birdpowers => 1) "pecking", 2) "x-ray vision", 3) "flight"	# n개의 set을 모두 결합(중복된 원소는 제거)


2. ordered set : 순서가 있는 set
  ZADD hackers 1940 “Alan Kay”		# 1940이 순서, “Alan Kay”가 value
  ZADD hackers 1906 "Grace Hopper"
  ZADD hackers 1953 "Richard Stallman"
  ZADD hackers 1965 "Yukihiro Matsumoto"
  ZADD hackers 1916 "Claude Shannon"
  ZADD hackers 1969 "Linus Torvalds"
  ZADD hackers 1957 "Sophie Wilson"
  ZADD hackers 1912 "Alan Turing"

  ZRANGE hackers 2 4 => 1) "Claude Shannon", 2) "Alan Kay", 3) "Richard Stallman"

  ZADD hackers 1916 “circlelcric” 		# 이미 1916순서가 존재해도 추가 됨
  ZRANGE hackers 2 4 => 1) "Claude Shannon", 2) “circlelcric “ 3) "Alan Kay"


3. hash : 테이블 형태로 저장이 가능
  HSET user:1000 name "John Smith"				# hash 타입의 데이터 설정(1개의 필드만 저장)
  HSET user:1000 email "john.smith@example.com"
  HSET user:1000 password "s3cret"

  HGET user:1000 email => 1) "john.smith@example.com"	# 특정 필드 값 반환
  HGETALL user:1000 => 1) “name”, 2) “John Smith”, 3) “email”, 4) "john.smith@example.com”, 5) “password”, 6) “s3cret”
  HSET user:1000 name “James Bond”				# 이미 존재하는 필드의 값 변경
  HGET user:1000 name => “James Bond”

  HMSET user:1001 name “kwy” age 29 sex “male” 		# 여러 필드를 한번에 설정

  숫자 형태의 필드 값은 다음과 같이 INCRBY를 사용해 값을 증가시킬 수 있음
  HSET user:1000 visits 10
  HINCRBY user:1000 visits 1 => 11			# 1 증가
  HINCRBY user:1000 visits 10 => 21		# 10 증가
  HDEL user:1000 visits
  HINCRBY user:1000 visits 1 => 1
