# Interpreter Implementation Lab 1

## Members
- Capinpuyan, Luigi
- Tan, Lance Andrei
- Trinidad, Leanne Ena F.

## Functionality
The group chose to implement set 1.

### Do-while loop
```
var x = 2;
do {
    print x;
    print x + 1;
} while(x < 12)
```

### Support the bang operator for numbers.
```
	@Override
	public Object visitUnaryExpr(Expr.Unary expr) {
		Object right = evaluate(expr.right);

	    switch (expr.operator.type) {
	        case BANG:
	            return !isTruthy(right);
	    	case MINUS:
	    		checkNumberOperand(expr.operator, right);
	    		return -(double)right;

	    }
	    // Unreachable.
	    return null;
	}
	
	private boolean isTruthy(Object object) {
		if (object instanceof Double &&  Double.parseDouble(stringify(object)) != 0) return true;
		if (Integer.parseInt(stringify(object)) == 0) return false;
		if (object == null) return false;
		if (object instanceof Boolean) return (boolean)object;
		return true;
	}
```

### Implement “ASMD”
```
print 5 * 3 + 2;
```
private Expr comparison() {
    Expr expr = factor();

    while (match(GREATER, GREATER_EQUAL, LESS, LESS_EQUAL)) {
      Token operator = previous();
      Expr right = factor();
      expr = new Expr.Binary(expr, operator, right);
    }

    return expr;
  }
  
  private Expr factor() {
	    Expr expr = term();

	    while (match(SLASH, STAR)) {
	      Token operator = previous();
	      Expr right = term();
	      expr = new Expr.Binary(expr, operator, right);
	    }

	    return expr;
	  }

  private Expr term() {
    Expr expr = unary();

    while (match(MINUS, PLUS)) {
      Token operator = previous();
      Expr right = unary();
      expr = new Expr.Binary(expr, operator, right);
    }

    return expr;
  }
