
-XX:+AggresiveHeap
-XX:+AgressiveOps

# Java 8
* Forçar o uso da mesma string caso sejam iguais (mesmo usando new String)
-XX:+UseStringDeduplicationJVM

* Uma thread
-XX:+UseSerialGC

* Várias threads
-XX:+UseConcMarkSweepGC

* Grande pool
-XX:+UseG1GC
