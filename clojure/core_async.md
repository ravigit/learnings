## Understanding core async

#### Puts

- '>!' puts a value on a channel. However, if the channel is unbuffered, the go-block is parked until there is a listener on the channel.
- '>!!' works outside a go block. Hence it blocks when the channel is unbuffered if there is not listener on the channel
- put! is asynchronous and returns immediately. Even with a unbuffered channel, it queues requests, and can be used without a listener


#### Takes

- <! reads a value from a channel. If the channel doesn't have any value in it, the go-block is parked until there is a value
- <!! works outside a go block. Hence it blocks when the channel doesn't have any value in it
- take! is asynchronous and takes a callback function as a parameter. This function is called with the value on the channel when it is available


#### Alt

- alts! takes a vector of channels and returns a pair, of which the first is the value on the channel and the second the channel itself.
- alts!! is same as above but works outside go-block
- alt! form is same as cond in that it takes clauses, each of which can be a channel, a vector of channels or a vector of vector containing a channel and a value. The last form is to put (not take) that value into the given channel. This makes sense in case of unbuffered channels with no listener, in which case the form can timeout or do something else.
```
(def ch (ca/chan))
(def ch2 (ca/chan))
(def ch3 (ca/chan))

(defn process []
   (let [v (ca/alt!!
              (ca/timeout 5000) :timedout
              [ch2 ch3] ([v c] (format "Read %s from %s" v c))
              [[ch 200]] :sent)]
     (println "Read alt!! :" v)))
     
(ca/put! ch2 20) (process)
;; Read alt!! : 20
(ca/take! ch (fn [x] (println "Take! :" x))) (process)
;; Read alt!! : :sent
;; Take! : 200
```
