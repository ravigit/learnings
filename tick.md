## Notes on Tick (Also Java Time API)

Tick is built on Java Time API. It has functions that allow converting and creating clojure friendly instances of that API.


### Java Time API
New Java Time API is designed to make working with dates less ambiguous by not overloading the concept of date with its representation in various formats, and trying to work across them. Instead it canonically separates concepts into Instant, LocalDateTime, OffsetDateTime and ZonedDateTime classes.

Instant is a dot on timeline. It has nothing to do with timezones or offsets or daylight savings. An instant *now* is now, the same value, in US or India or Japan. It is possible to do all the operations on instant, just as if it is a point on an axis ie, subtracting from another instant, comparison, adding a period to a instant to advance it etc. For all purposes it can be treated like nanos from epoch, and has methods that allow that conversion.

LocalDateTime is abstract representation of a date and time. Say *2022-03-07T13:05:32.994493*, but it is not a string, but a date on calendar and a time on clock. Like instant, it offers methods to add and subtract periods to it, compare with other localdatetimes. Note that this is abstract representation of a date, and is not real enough to be able to convert to epoch millis or seconds.

OffsetDateTime and ZonedDateTime bring LocalDateTime to reality ie, they can be converted to epoch millis or instant. LocalDateTime has methods that take a zone or offset to convert it to OffsetDateTime and ZonedDateTime respectively. The difference between them is, ZonedDateTime accounts for daylight savings, because it specifies both offset and the zone, for example, *2022-04-08T11:31:12.994391-07:00[America/Los_Angeles]*


### Tick

```clojure
  (require ['tick.alpha.api :as t])

  (t/now) ;; This is Java API Instant *now*
  ;; => #time/instant "2022-04-08T18:45:55.827072Z"

  (t/inst (t/now)) ;; This is old Java Date 
  ;; => #inst "2022-04-08T18:46:18.319-00:00"

  (t/epoch)
  ;; => #time/instant "1970-01-01T00:00:00Z"

  (t/millis (t/between (t/epoch) (t/now)))
  ;; => 1649444500366
  
  (t/date-time "2022-03-07T13:05:32.994493")
  ;; => #time/date-time "2022-03-07T13:05:32.994493"

  (t/in (t/date-time "2022-03-07T13:05:32.994493") (t/zone "America/Los_Angeles"))
  ;; => #time/zoned-date-time "2022-03-07T13:05:32.994493-08:00[America/Los_Angeles]"

  (t/offset-date-time "2022-03-07T13:05:32.994493-08:00")
  ;; => #time/offset-date-time "2022-03-07T13:05:32.994493-08:00"

  (t/offset-by (t/date-time "2022-03-07T13:05:32.994493") "-08:00")
  ;; => #time/offset-date-time "2022-03-07T13:05:32.994493-08:00"

  (t/< (t/offset-date-time "2022-03-07T13:05:32.994493-08:00")
       (t/offset-date-time "2021-03-07T13:05:32.994493-08:00"))
  ;; => false
  
  (t/instant (t/offset-date-time "2022-03-07T13:05:32.994493-08:00"))
  ;; => #time/instant "2022-03-07T21:05:32.994493Z"
  
  (t/< (t/instant (t/offset-date-time "2022-03-07T13:05:32.994493-08:00"))
       (t/now)) 
  ;; => true

  (ta/new-duration 1 :days)
  ;; => #time/duration "PT24H"

  (ta/new-duration 1 :months)
  ;; java.time.temporal.UnsupportedTemporalTypeException

  (ta/new-period 1 :months)
  ;; => #time/period "P1M"

  (ta/new-period 1 :years)
  ;; => #time/period "P1Y"

  (ta/new-period 1 :days)
  ;; => #time/period "P1D"

  (ta/new-period 1 :hours)
  ;;  java.lang.IllegalArgumentException

  (t/+ (t/offset-date-time "2022-03-07T13:05:32.994493-08:00")
       (t/new-duration 1 :days))
  ;; => #time/offset-date-time "2022-03-08T13:05:32.994493-08:00"

  (t/+ (t/offset-date-time "2022-03-07T13:05:32.994493-08:00")
       (t/new-period 1 :days))
  ;; => #time/offset-date-time "2022-03-08T13:05:32.994493-08:00"    

  (t/- (t/date-time "2022-03-07T13:05:32.994493")
       (t/new-duration 1 :hours))
  ;; => #time/date-time "2022-03-07T12:05:32.994493"

  (t/- (t/offset-date-time "2022-03-07T13:05:32.994493")
       (t/new-duration 1 :months))
  ;; java.time.temporal.UnsupportedTemporalTypeException

  (t/+ (t/offset-date-time "2022-03-07T13:05:32.994493-08:00")
       (t/new-period 1 :months))
  ;; => #time/offset-date-time "2022-04-07T13:05:32.994493-08:00"

  (t/+ (t/now) (t/new-period 1 :days))
  ;; => #time/instant "2022-04-09T19:15:48.524101Z"

  (t/+ (t/new-period 1 :days) (t/new-period 1 :years))
  ;; => #time/period "P1Y1D"

  (t/>> (t/offset-date-time "2022-03-07T13:05:32.994493-08:00")
        (t/new-period 1 :months))
  ;; => #time/offset-date-time "2022-04-07T13:05:32.994493-08:00"
  
  ;; Convert millis into local date time
  (t/in (t/instant 1665092442171) (t/zone))
  ;; => #time/zoned-date-time "2022-10-06T14:40:42.171-07:00[America/Los_Angeles]"
  


```

