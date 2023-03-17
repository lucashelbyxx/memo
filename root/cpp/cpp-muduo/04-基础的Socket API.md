# What performance do we care?

- Bandwitdh, MB/s
- Throughput(吞吐量), message /s, queries /s (QPS), transactions /s (TPS)
- Latency,milliseconds, percentiles
- Utilization, percent, payload vs. carrier, goodput vs. theory BW
- Overhead, eg. CPU usage, for compression and/or encryption



# Why do we re-implement TTCP?

- it uses all basic sockets APIs: socket, listen, bind, accept, connect, read/recv, write/send, shutdown, close, etc.
- the protocol is binary, not just byte stream, so it's better than the classic echo example
- typical behaviors, meaningful results, instead of packets /s
- service as benchmark(基准) of programming language as well, by comparing CPU usage
- not concurrent, at least in the very basic form



#  The Code

- Straight forward with blocking IO
  - muduo / examples / ace / ttcp / ttcp_blocking.cc (C with sockets API)
  - recipes / tpc / ttcp.cc (C++ with a thin wrapper)
  - muduo-examples-in-go / examples / ace / ttcp / ttcp.go (Go)
- Non-blocking IO with muduo library
  - muduo / examples / ace / ttcp / ttcp.cc
- None of above support concurrent connections
  - pretty easy to enable, thread-per-connection for first three.