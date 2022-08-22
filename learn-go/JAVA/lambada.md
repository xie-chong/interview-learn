## 从List中按固定大小取数

```
List<String> repayDueBillNoLists = List.of("");
        int partialLimit = 1000;
        int limit = (repayDueBillNoLists.size() + partialLimit - 1) / partialLimit;
        Stream.iterate(0, n -> n + 1).limit(limit)
                .parallel()
                .forEach(i -> {
                    List<String> subDueBillNos = repayDueBillNoLists.stream()
                            .skip(i * partialLimit).limit(partialLimit).collect(Collectors.toList());
                });
```
