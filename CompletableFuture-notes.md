<h3>CompletableFuture - </h3>




<h4>thenSupplyAsync -</h4> 

A factory method provided by `CompletableFuture` class. It takes a `Supplier` as argument and returns a `CompletableFuture` that will be
completed asynchronously with the value obtained by invoking the `Supplier`.

e.g. -
```
public Future<Double> getPriceAsync(String product) {
	return CompletableFuture.supplyAsync( () -> calculatePrice(product) );
}
```



<h4>thenApply -</h4>

`CompletableFuture<U> thenApply(Function <? super T, ? extends U> func)`

Used for transformation purpose. Returns a new CompletionStage that, when _this_ stage completes normally, is executed with _this_ stage's result as the argument to the supplied function.




<h4>thenCompose -</h4>

Allow you to pipeline 2 asynchronous operations, passing the result of the first operation to the second operation when it completes.

You can compose 2 `CompletableFutures` by invoking `thenCompose` on the first CF and passing it a `Function` 
which takes value returned by the first CF(*when it completes*) and returns a second CF that uses the result from the first CF as an input for its computation. 

`<U> CompletableFuture<U> theCompose(Function< ? super T, ? extends CompletionStage<U> func>`

```
public List<String> findPrices(String product) {

    List<CompletableFuture<String>> priceFutures = 
            shops.stream()
                 .map(shop -> CompletableFuture.supplyAsync(() -> shop.getPrice(product), executor))
                 .map(future -> future.thenApply(Quote::parse))
                 .map(future -> future.thenCompose(quote -> CompletableFuture.supplyAsync(() -> Discount.applyDiscount(quote), executor)))
                 .collect(Collectors.toList())

            return priceFutures.stream()
                               .map(CompletableFuture::join)
                               .collect(Collectors.toList())

  }
```


<h4>thenCombine -</h4> 
Used to combine to independent task (completable futures)

`CompletableFuture<V> thenCombine( CompletableFuture<U> cf , BiFunction < T, U, V > func )`

The CF on which this method is called on is : `CompletableFuture<T>`.

The `BiFunction` is used to combine result from first completable future ( on which theCombine is called ) and the second completable future.


If you use `thenCombineAsync` then the task to combine (`BiFunction`) is called on a different thread.

<h6>example code:</h6>

```
int x = 8888;
ExecutorService service = Executors.newFixedThreadPool(10);

CompleteableFuture<Integer> a = new CompleteableFuture<>();
CompleteableFuture<Integer> b = new CompleteableFuture<>();

CompleteableFuture<Integer> c = a.thenCombine(b, (y , z) -> y + z);
// in above, y and z are the output of a and b CF
service.submit(() -> a.complete(f(x)));
service.submit(() -> b.complete(g(x)));

Sysout(c.get()); // result of y + z
service.shutdown();
```



