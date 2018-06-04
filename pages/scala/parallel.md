## Parallel collections

scala>  import scala.collection.parallel._
import scala.collection.parallel._

scala>  val pc = mutable.ParArray(1, 2, 3)
pc: scala.collection.parallel.mutable.ParArray[Int] = ParArray(1, 2, 3)

scala> pc.tasksupport = new ForkJoinTaskSupport(new scala.concurrent.forkjoin.ForkJoinPool(2))
warning: there was one deprecation warning (since 2.12.0); for details, enable `:setting -deprecation' or `:replay -deprecation'
pc.tasksupport: scala.collection.parallel.TaskSupport = scala.collection.parallel.ForkJoinTaskSupport@59bdcf98

scala> pc map { _ + 1 }