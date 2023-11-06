# Better Code Refactoring 
   
  we need to call topupservice by operator.
  
  Before::
  
  var response = callTopUpServiceByOperator(topUpRequest, Operator.MPT);
   
    @Getter
    @NoArgsConstructor
    @AllArgsConstructor
    public enum Operator {
    
        MPT(""),
        ATOM(""),
        OOREDOO(""),
        MYTEL("");
        
        private final String value;
    
    }
    
    private TopUpResponse callTopUpServiceByOperator(TopUpRequest topUpRequest, Operator operator) {
        switch (operator) {
            case MPT:
                return topUpTransactionService.mptTopUp(topUpRequest);
            case ATOM:
                return topUpTransactionService.atomTopUp(topUpRequest);
            case OOREDOO:
                return topUpTransactionService.ooredooTopUp(topUpRequest);
            case MYTEL:
                return topUpTransactionService.mytelTopUp(topUpRequest);
        }
        return null; //we should return null value
    }

After::
var response = callTopUpServiceByOperator(topUpRequest, Operator.MPT);

    @Getter
    @NoArgsConstructor(force = true)
    public enum Operator {
    
        MPT(TopUpTransactionService::mptTopUp),
        ATOM(TopUpTransactionService::atomTopUp),
        OOREDOO(TopUpTransactionService::ooredooTopUp),
        MYTEL(TopUpTransactionService::mytelTopUp);
    
        Operator(BiFunction<TopUpTransactionService, TopUpRequest, TopUpResponse> algo) {
            this.algo = algo;
        }
    
        public final BiFunction<TopUpTransactionService, TopUpRequest, TopUpResponse> algo; //we can use BiFunction in Java 8
    }

    public class TopUpTransactionService {   
        public TopUpResponse callTopUpServiceByOperator(TopUpRequest topUpRequest, Operator operator) {
            return operator.topUpAlgo.apply(this, operator);
        }
    }
