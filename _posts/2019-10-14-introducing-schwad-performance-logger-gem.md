---
 layout: post
 title:  "Introducing my Schwad Performance Logger gem"
 date:   2019-10-14 07:34:09 +0000
 categories: ruby rails gem performance
 comments: true
---

{: style="text-align:center"}
![Favorite](https://i.imgur.com/gdeS97j.jpg)

Do you find yourself writing `puts` statements all throughout your code - only to check which chunks are burning the most time (or memory)?

```ruby
puts '*' * 300
start_time = Time.now
# your actual code
time_elapsed = Time.now - start_time
puts "Time elapsed is: #{time_elapsed}"
puts '*' * 300
```

Do you also find yourself manually recording these values from your terminal to later include in a PR explaining how you've improved code performance?

Further, how often do you reach for one of half-a-dozen different performance profiling tools to get simple measurements?

If one or more of these is you - then I think you're going to like my nifty little gem I've just open-sourced: [SchwadPerformanceLogger](https://github.com/Schwad/schwad_performance_logger)

## So what does it do?

This gem allows you to log memory and time usage throughout your code quickly- and on your terms. It also gives you very quick access to basic features of a handful of other (fantastic) performance tracking tools.

## Logging time and memory

In your code:

```ruby
pl = SPL.new({full_memo: 'Check extract method refactoring'})
```

In your terminal:
```
**********************************************************************
Starting initialization. Current memory: 12(Mb), difference of 0 (mb) since beginning and difference of 0 since last log. time passed: 0.004678 seconds, time since last run: 0.004678
**********************************************************************
```

The object is now 'live', anywhere in your file call the object again with a memo of your choice:

```ruby
pl.log_performance("Test memo")
```

```
*********************************************************************
Starting Test memo. Current memory: 12(Mb), difference of 0 (mb) since beginning and difference of 0 since last log. time passed: 22.493993 seconds, time since last run: 9.616874
*********************************************************************
```

As you can see you can track the time since the object was last called, same with memory. You also get a running track of the total time elapsed and memory grown.

__(NOTE: Remember Ruby is garbage collected, so hopefully memory doesn't go up forever - if it does this gem will help you debug it!)__

These logs can be written to the terminal, their own logfile, and even a CSV file in real time so you don't have to write them down.

### Options:

`full_memo` option adds an extra header in the `log` outputs as well as a header to each new set of csv outputs. This is not to be confused with the 'per-run' message passed to `#log_performance` which is only passed to that check.

To disable any of the outputs:

`SPL.new({puts: false, log: false, csv: false})`

To have the logger 'pause' a number of seconds during the `puts` logging so that
you can actually see the log as it goes by. This does not affect the 'time' measurement:

`SPL.new({pause: 8})`

## Further Profiling Tools

As well as logging memory and time throughout your code, SPL gives you easy access to frequently used popular profiling tools to inspect your code blocks. Note - if you want to a deep dive on a particular area of profiling I recommend visiting the gems listed below and giving them a whirl.

### IPS

Handy access to [Benchmark-ips](https://github.com/evanphx/benchmark-ips) measurements, just pass a block to ips:

```ruby
SPL.ips do
   ary = []
   35.times do
     ary << (1..99).to_a.sample
   end
end

#=> #<Benchmark::IPS::Report:0x00007fbc7f91df50 @entries=[#<Benchmark::IPS::Report::Entry:0x00007fbc7e0c3bd0 @label="PerformanceLogMethod", @microseconds=5002798.0, @iterations=34020, @stats=#<Benchmark::IPS::Stats::SD:0x00007fbc7e0c3c48 @mean=6805.780564500376, @error=195>, @measurement_cycle=630, @show_total_time=true>], @data=nil>
```

### Time

Same flow as above. Tired of writing out `start_time` and `Time.now - start_time` and also needing to 'puts' it out? Pass a block to `#time`. Runs ten times and spits out an average as well.

```ruby
SPL.time do
   ary = []
   35.times do
     ary << (1..99).to_a.sample
   end
end

#=> Average runtime 0.0002649 seconds. Max time 0.000508.seconds
```

### Allocate Count

Before, you would have to enable the `GC` before your code, use `ObjectSpace` to count objects before your code, then use it again after your code to compare allocated objects during your block of code. You'd also have to re-enable the `GC`! Gosh, that sure is a lot of work if you want to do this frequently. We make it simple.

```ruby
SPL.allocate_count do
   ary = []
   35.times do
     ary << (1..99).to_a.sample
   end
end

#=> {:FREE=>-121, :T_STRING=>50, :T_ARRAY=>36, :T_IMEMO=>35}
```

### Profile Memory

Gives you quick access to the amazing [memory_profiler](https://github.com/SamSaffron/memory_profiler) gem.

```ruby
SPL.profile_memory do
   ary = []
   35.times do
     ary << (1..99).to_a.sample
   end
end

# Total allocated: 37576 bytes (36 objects)
# Total retained:  0 bytes (0 objects)
#
# allocated memory by gem
# -----------------------------------
#      37576  other
#
# allocated memory by file
# -----------------------------------
#      37576  (irb)
#
# allocated memory by location
# -----------------------------------
#      37240  (irb):37
#        336  (irb):35
#
# allocated memory by class
# -----------------------------------
#      37576  Array
#
# allocated objects by gem
# -----------------------------------
#         36  other
#
# allocated objects by file
# -----------------------------------
#         36  (irb)
#
# allocated objects by location
# -----------------------------------
#         35  (irb):37
#          1  (irb):35
#
# allocated objects by class
# -----------------------------------
#         36  Array
```

### All Objects

Similarly, it's nice to get a rundown of all objects, in hash format, instead of goofing around with `ObjectSpace` manually we offer that up for you as well.

```ruby
SPL.all_objects do
  ary = []
  35.times do
    ary << (1..99).to_a.sample
  end
end

#=> [[Benchmark::IPS::Job, 1], [Rational, 1], [Benchmark::IPS::Report::Entry, 1], [Benchmark::IPS::Stats::SD, 1], [FFI::DynamicLibrary, 1], [DidYouMean::ClassNameChecker, 1], [Thread::Backtrace, 1], [NameError::message, 1], [NameError, 1], [#<Class:0x00007fbc7e816478>, 1], [Gem::Platform, 1], [IRB::Notifier::CompositeNotifier, 1], [IRB::Notifier::NoMsgNotifier, 1], [Enumerator, 1], [RubyToken::TkSPACE, 1], [FFI::Type::Mapped, 1], [IRB::ReadlineInputMethod, 1].... etc
```

### Objects by Size

You can break down your objects by size as well, useful for debugging.

```ruby
SPL.objects_by_size do
  ary = []
  35.times do
    ary << (1..99).to_a.sample
  end
end

#=> {:T_OBJECT=>101848, :T_CLASS=>730344, :T_MODULE=>76808, :T_FLOAT=>240, :T_STRING=>882168, :T_REGEXP=>200350, :T_ARRAY=>714384, :T_HASH=>150408, :T_STRUCT=>800, :T_BIGNUM=>80, :T_FILE=>1160, :T_DATA=>1074338, :T_MATCH=>28280, :T_COMPLEX=>40, :T_RATIONAL=>40, :T_SYMBOL=>5080, :T_IMEMO=>325040, :T_ICLASS=>3280, :TOTAL=>4294688}
```

<form action="https://www.getdrip.com/forms/275494850/submissions" method="post" data-drip-embedded-form="275494850">
  <h3 data-drip-attribute="headline">Stay in Touch</h3>
  <div data-drip-attribute="description">I like to write about Ruby and building things, typically once every month or so. Get an email when I have written something new.</div>
    <div>
        <label for="drip-email">Email Address</label><br />
        <input type="email" id="drip-email" name="fields[email]" value="" />
    </div>
  <div>
    <input type="submit" value="I Love Ruby too! 💎" data-drip-attribute="sign-up-button" />
  </div>
</form>

<!-- Drip -->
<script type="text/javascript">
  var _dcq = _dcq || [];
  var _dcs = _dcs || {};
  _dcs.account = '2671646';

  (function() {
    var dc = document.createElement('script');
    dc.type = 'text/javascript'; dc.async = true;
    dc.src = '//tag.getdrip.com/2671646.js';
    var s = document.getElementsByTagName('script')[0];
    s.parentNode.insertBefore(dc, s);
  })();
</script>
<!-- end Drip -->
