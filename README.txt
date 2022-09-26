# Interpreter Implementation Lab 1

## Members
- Capinpuyan, Luigi
- Tan, Lance Andrei
- Trinidad, Leanne Ena F.

## Functionality
The group chose to implement set 1.

### Do-while loop
```
Interpreter.java
 @Override
	  public Void visitDoWhileStmt(Stmt.DoWhile stmt) {
		execute(stmt.body);
	    while (isTruthy(evaluate(stmt.condition))) {
	      execute(stmt.body);
	    }
	    return null;
	  }
Parser.java
if (match(DO)) return doWhileStatement();

...

  private Stmt doWhileStatement() {
	  	Stmt var_dec = varDeclaration();							// used the variable declaration statement object
	  	
		consume(LEFT_BRACE, "Expect '{' after 'do'.");					
		Stmt doStatement = statement();
		consume(RIGHT_BRACE, "Expect '}' after do statement.");
		
		consume(LEFT_PAREN, "Expect '(' after 'while'.");
	    Expr condition = expression();
	    consume(RIGHT_PAREN, "Expect ')' after condition.");
		consume(SEMICOLON, "Expect ';' after while statement.");
		return new Stmt.While(condition, doStatement);
	}

Scanner.java
    keywords.put("do",     DO);
    
Stmt.java
R visitDoWhileStmt(DoWhile stmt);

GenerateAst.java
"DoWhile    : Stmt body, Expr condition"

...

static class DoWhile extends Stmt {
	    DoWhile(Stmt body, Expr condition) {
	      this.condition = condition;
	      this.body = body;
	    }

	    @Override
	    <R> R accept(Visitor<R> visitor) {
	      return visitor.visitDoWhileStmt(this);
	    }

	    final Expr condition;
	    final Stmt body;
	  }

TokenType.java
// added "DO" to keywords

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
