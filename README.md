# Q2-DAA-E
Quiz 2 by Group 13 DAA E
we make this project using java with netbeans IDE.
any explanation was showed in pdf report, so we will not show the explanation again here.
Only source code and command will showed here.


source code:

	Puzzle.java
	package puzzle;
	
	import java.util.logging.Level;
	import java.util.logging.Logger;
	import javax.swing.UIManager;
	import javax.swing.UnsupportedLookAndFeelException;
	
	/**
	 * contains only the main method that starts the GUI
	 */
	public class Puzzle {
	
	    /**
	     * @param args the command line arguments
	     */
	    public static void main(String[] args){
	        
	        //set the look and feel to system's look and feel
	        try {
	            UIManager.setLookAndFeel(UIManager.getSystemLookAndFeelClassName());
	        } catch (ClassNotFoundException | InstantiationException | IllegalAccessException | UnsupportedLookAndFeelException ex) {
	            Logger.getLogger(GUI.class.getName()).log(Level.SEVERE, null, ex);
	        }
	        
	        //start the gui.
	        GUI gui = new GUI();
	        gui.setVisible(true);
	        
	    }
	    
	}
	

	Analysis :
•	We made this class to support the main method that starts the “GUI”
•	We set the “look and feel” from our program to be easier to use.
try {
            UIManager.setLookAndFeel(UIManager.getSystemLookAndFeelClassName());
        } catch (ClassNotFoundException | InstantiationException | IllegalAccessException | UnsupportedLookAndFeelException ex) {
            Logger.getLogger(GUI.class.getName()).log(Level.SEVERE, null, ex);
        }


	BoardControl.java
	package puzzle;
	
	import java.awt.event.ActionEvent;
	import java.awt.event.ActionListener;
	import java.util.Arrays;
	import java.util.Map;
	import java.util.Random;
	import java.util.Stack;
	import javax.swing.Timer;
	
	/**
	 * BoardControl Class
	 * Contains all the methods that manipulates the board
	 */
	public class BoardControl{
	    
	    public static enum MOVES{UP, DOWN, RIGHT, LEFT};
	    public static enum SPEED{SLOW, MEDIUM, FAST};
	    private int timerSpeed = 500;
	    public static final byte[] GOAL = {1, 2, 3, 4, 5, 6, 7, 8, 0};
	    private byte[] current = {1, 2, 3, 4, 5, 6, 7, 8, 0};
	    private boolean solving = false;
	    
	    public boolean isSolving(){
	        return this.solving;
	    }
	    
	    public byte[] getCurrentBoard(){
	        return current.clone();
	    }
	    
	    public void setCurrentBoard(byte[] b){
	        this.current = b;
	    }
	    
	    public void setTimerSpeed(SPEED speed){
	        switch(speed){
	            case SLOW:
	                this.timerSpeed = 700;
	                break;
	            case MEDIUM:
	                this.timerSpeed = 300;
	                break;
	            case FAST:
	                this.timerSpeed = 100;
	                break;
	        }
	    }
	    
	    //handles the tiles' press
	    //figures out the tile's position relative to the blank and moves accordingly
	    public void tilePressed(int btn){
	        int blank = getBlankIndex(current);
	        if(btn == blank-1){
	            move(current, MOVES.LEFT);
	        }else if(btn == blank+1){
	            move(current, MOVES.RIGHT);
	        }else if(btn == blank+3){
	            move(current, MOVES.DOWN);
	        }else if(btn == blank-3){
	            move(current, MOVES.UP);
	        }
	    }
	    
	    //make a move on the given board, changes the given board
	    //if the move is invalid, do nothing
	    //Note that the move is according to the blank, i.e. the blank moves UP or DOWN and so on
	    public static void move(byte[] board, MOVES toMove){
	        int blank = getBlankIndex(board);
	        if(blank == -1) return;  
	        switch(toMove){
	            case UP:
	                if(blank/3 != 0) swap(board, blank, blank-3);
	                break;
	            case DOWN:
	                if(blank/3 != 2) swap(board, blank, blank+3);
	                break;
	            case RIGHT:
	                if(blank%3 != 2) swap(board, blank, blank+1);
	                break;
	            case LEFT:
	                if(blank%3 != 0) swap(board, blank, blank-1);
	                break;
	        }
	    }
	    
	    public boolean isSolved(){
	        return Arrays.equals(this.current, this.GOAL);
	    }
	    
	    //resets the board
	    public void resetBoard(){
	        for(int i = 0 ; i < current.length-1 ; ++i) current[i] = (byte)(i+1);
	        current[current.length - 1] = 0;
	    }
	    
	    //generates a random, solvable board and makes it the current board
	    public void randomizeBoard(){
	        byte board[];
	        while(!isSolvable(board = getRandomBoard()));
	        current = board;
	    }
	    
	    //makes a state at random, not necessarly solvable
	    private byte[] getRandomBoard(){
	        boolean f[] = new boolean[current.length];
	        byte board[] = new byte[current.length];
	        Random rand = new Random();
	        
	        //randomizes each element and make sure no element is repeated
	        for(int i = 0 ; i < current.length ; ++i){
	            byte t;
	            while(f[t = (byte)rand.nextInt(9)]);
	            f[t] = true;
	            board[i] = t;
	        }
	        return board;
	    }
	    
	    //checks if the given state is solvable or not
	    private boolean isSolvable(byte board[]){
	        //inversion counter
	        int inv = 0;
	        for(int i = 0 ; i < board.length ; ++i){
	            if(board[i] == 0) continue;
	            for(int j = i+1 ; j < board.length ; ++j){
	                //wrong precedence, count an inversion
	                if(board[j] != 0 && board[i] > board[j]) ++inv;
	            }
	        }
	        
	        //the board is solvable if the number of inversions is even
	        return (inv % 2 == 0);
	    }
	    
	    //returns the index of the blank element in the given board, -1 if not found (impossible case)
	    public static int getBlankIndex(byte[] board){
	        for(int i = 0 ; i < board.length ; ++i) if(board[i] == 0) return i;
	        return -1;
	    }
	    
	    //swaps 2 elemets in the given board
	    //if the swap is impossible (index out of range), do nothing
	    public static void swap(byte[] board, int i, int j){
	        try{
	            byte iv = board[i];
	            byte jv = board[j];
	            board[i] = jv;
	            board[j] = iv;
	        }catch(ArrayIndexOutOfBoundsException ex){
	            //if i or j is out of range, do nothing
	        }
	    }
	    
	    //used for debugging
	    public static void print(byte[] b){
	        for(int i = 0 ; i < b.length ; ++i)
	                System.out.print(b[i] + " ");
	            System.out.println("");
	    }
	    
	    public void solve(GUI gui, Solvers.SOLVE_METHOD method){
	        
	        Map<String, byte[]> parent = null;
	        
	        this.solving = true;
	        
	        long time = System.nanoTime();
	        switch(method){
	            case A_STAR:
	                parent = Solvers.aStar(getCurrentBoard().clone());
	                break;
	            case DFS:
	                parent = Solvers.dfs(getCurrentBoard().clone());
	                break;
	        }
	        
	        time = (System.nanoTime() - time) / 1000000;
	        
	        //use backtracking like technique to get the moves to be made
	        //solution states (not moves) are saved into the stack in order to be executed
	        Stack<byte[]> nextBoard = new Stack<>();
	        nextBoard.add(GOAL.clone());
	        while(!Arrays.equals(nextBoard.peek(), this.current))
	            nextBoard.add(parent.get(make(nextBoard.peek())));        
	        nextBoard.pop();
	        
	        String status = String.format("<html>%d ms<br/>%d moves<br/>%d expanded nodes</html>", time, nextBoard.size(), Solvers.times);
	        gui.setStatus(status);
	        
	        //start a timer to make a move every given time until the board is solved
	        new Timer(this.timerSpeed, new ActionListener(){
	            private Stack<byte[]> boards;
	            public BoardControl bc;
	            
	            //gives the timer the stack of states, the gui and the board controller
	            //and disables the whole GUI untill finished
	            public ActionListener me(Stack<byte[]> stk, BoardControl _bc){
	                this.boards = stk;
	                this.bc = _bc;
	                return this;
	            }
	            
	            @Override
	            public void actionPerformed(ActionEvent e) {
	                
	                //if the stack is empty, close enable the GUI and stop the timer
	                if(boards.empty() || isSolved()){
	                    BoardControl.this.solving = false;
	                    ((Timer)e.getSource()).stop();
	                    return;
	                }
	                
	                //set the current board to the given state and update the GUI
	                bc.setCurrentBoard(boards.pop());
	                gui.drawBoard();
	            }
	        }.me(nextBoard, this)).start();    //start the timer right away
	    }
	    
	    //takes an array of byte and makes it into a string and returns the string
	    //used for the hashing
	    private String make(byte[] arr){
	        String str = "";
	        for(int i = 0 ; i < arr.length ; ++i){
	            str += String.valueOf(arr[i]);
	        }
	        return str;
	    }
	    
	}
	

	Analysis :
•	This is BoardControl class, this class contains all the methods that manipulates the board
•	In this class we make the “movement”, in this part the movement will fill the blank area of the puzzle according to  its goal.
•	We use the A* and DFS methods to solve this puzzle.
•	We also set the timer to know how long the execution of  a program.
•	We make the “randomize” function, to randomize the puzzle.
•	We also make the “reset” function to give it back to the starts.

	Solvers.java
	package puzzle;
	
	import java.util.Arrays;
	import java.util.HashMap;
	import java.util.HashSet;
	import java.util.Map;
	import java.util.PriorityQueue;
	import java.util.Set;
	import java.util.Stack;
	
	/**
	 *
	 * 
	 * This class contains the implementation of the methods needed for solving the current state
	 * Both methods implemented here return a hash map that contains the parent state
	 * of each state on the way for solving the current state
	 */
	public class Solvers {
	    
	    public static enum SOLVE_METHOD{A_STAR, DFS};
	   
	    //global variables needed for DFS
	    private static final Map<String, byte[]> dfs_parent = new HashMap<>();
	    private static final Set<String> dfs_vis = new HashSet<>();
	    
	    //to count the number of expanded nodes
	    public static long times;
	    
	    //solve the current position with A* search
	    public static Map<String, byte[]> aStar(byte[] current){
	        PriorityQueue<State> q = new PriorityQueue<>();
	        Map<String, Integer> dist = new HashMap<>();
	        Map<String, byte[]> parent = new HashMap<>();
	        
	        times = 0;
	        
	        //intialize the distance of the current state to be 0
	        dist.put(stringify(current), 0);
	        
	        //add the current state to the front of the states queue
	        q.add(new State(current, 0));
	        
	        //A* Algorithm ...
	        while(!q.isEmpty()){
	            State crnt = q.poll();
	            times++;
	            if(Arrays.equals(crnt.getBoard(), BoardControl.GOAL)) break;            
	            for(State child : crnt.getNextStates()){
	                if(dist.getOrDefault(stringify(child.getBoard()), Integer.MAX_VALUE) > child.getCost()){                    
	                    parent.put(stringify(child.getBoard()), crnt.getBoard());
	                    dist.put(stringify(child.getBoard()), child.getCost());
	                    q.add(child);
	                }
	            }
	        }
	        
	        return parent;
	    }
	    
	    //Solve the game with DFS (Very very very inefficient)
	    //this method uses a stack to simulate the recursion
	    public static Map<String, byte[]> dfs(byte[] current){
	        Stack<State> stack = new Stack<>();
	        Map<String, byte[]> parent = new HashMap<>();
	        Set<String> vis = new HashSet<>();
	        
	        times = 0;
	        
	        //add the current state to the front of the states queue
	        stack.push(new State(current, 0));
	        
	        //the simlated recursion part
	        while(!stack.isEmpty()){
	            State crnt = stack.pop();
	            vis.add(stringify(crnt.getBoard()));
	            times++;
	            if(Arrays.equals(crnt.getBoard(), BoardControl.GOAL)) break;            
	            for(State child : crnt.getNextStates()){
	                if(vis.contains(stringify(child.getBoard()))) continue;
	                parent.put(stringify(child.getBoard()), crnt.getBoard());
	                stack.push(child);
	            }
	        }
	        
	        return parent;
	    }
	    
	    //takes a byte array and returns it as a string for the map to hash
	        public static String stringify(byte[] arr){
	        String str = "";
	        for(int i = 0 ; i < arr.length ; ++i){
	            str += String.valueOf(arr[i]);
	        }
	        return str;
	    }
	    
	}
	

	Analysis :
•	In this class we make function to solve the problem(puzzle) with A* and DFS methods
•	This function(A* and DFS) will be used in BoardControl class.

	State.java
	package puzzle;
	
	import java.util.ArrayList;
	import java.util.Arrays;
	
	/**
	 * Describes board states, used in the A* search only
	 * implemets comparator that compare the total weight of the states (cost + heurestic)
	 */
	public class State implements Comparable<State>{
	    
	    private final byte board[];     //stat's board
	    private final int cost;         //the cost needed to reach this state
	    private final int weight;       //the total weight of this state
	    
	    public State(byte b[], int _cost){
	        this.board = b;
	        cost = _cost;
	        weight = cost + hurestic();
	    }
	    
	    public byte[] getBoard(){
	        return this.board;
	    }
	    public int getCost(){
	        return this.cost;
	    }
	    
	    //calculates the heuristic of this board using manhattan distance
	    private int hurestic(){
	        int h = 0;
	        for(int i = 0 ; i < board.length ; ++i){
	            if(board[i] == 0) continue;
	            int dr = Math.abs(i/3 - (board[i]-1)/3);
	            int dc = Math.abs(i%3 - (board[i]-1)%3);
	            h += dr + dc;
	        }
	        return h;
	    }
	    
	    //generates and returns all the possible states from this state
	    public ArrayList<State> getNextStates(){
	        ArrayList<State> states = new ArrayList<>();
	                
	        //try every move, if a move changes the board, then it's a valid move 
	        for(BoardControl.MOVES move : BoardControl.MOVES.values()){
	            byte newBoard[] = this.board.clone();
	            BoardControl.move(newBoard, move);
	            if(!Arrays.equals(this.board, newBoard)){
	                states.add(new State(newBoard, this.cost + 1));
	            }
	        }
	        return states;
	    }
	
	    //compares the states using the total weight
	    @Override
	    public int compareTo(State o) {
	        return this.weight - o.weight;
	    }
	    
	}
	
	Analysis :
•	This class made to support the A* method from the Solvers class
•	This class contain function that will implemets comparator that compare the total weight of the states (cost + heurestic)
•	This class contain function that will calculates the heuristic of this board using manhattan distance too.

	GUI.java
	package puzzle;
	
	import java.awt.event.ActionEvent;
	import java.awt.event.ActionListener;
	import javax.swing.JButton;
	
	/** 
	 * The GUI of the game and everything that manipulates it
	 * (Except the timer that is in the BoardControl class)
	 */
	public class GUI extends javax.swing.JFrame {
	    
	    public final BoardControl boardControl;
	    private final JButton tiles[];
	    //private final BasicPlayer player;
	    
	    /**
	     * Creates new form GUI
	     */
	    public GUI() {
	
	        //frame Title
	        super("8 Puzzle");
	        
	        //set GUI settings
	        initComponents();
	        this.setLocationRelativeTo(null);   //center the frame in the screen on open
	        this.setResizable(false);
	        this.setAlwaysOnTop(true);
	        
	        //Global variabels intialization
	        this.tiles = new JButton[]{Tile_1, Tile_2, Tile_3, Tile_4, Tile_5, Tile_6, Tile_7, Tile_8, Tile_0};
	        this.boardControl = new BoardControl();
	        
	        //intialize the tiles, set the font, disable focus and add the action listener
	        for(int i = 0 ; i < tiles.length ; ++i){
	            
	            tiles[i].setFocusable(false);
	            tiles[i].setFont(tiles[i].getFont().deriveFont(25.0f));
	            
	            tiles[i].addActionListener(new ActionListener() {
	                
	                int num;
	                
	                ActionListener me(int i){
	                    num = i;
	                    return this;
	                }
	                
	                @Override
	                public void actionPerformed(ActionEvent e) {
	                    if(GUI.this.boardControl.isSolving()) return;
	                    GUI.this.boardControl.tilePressed(num);
	                    GUI.this.drawBoard();
	                }
	            }.me(i));
	        }
	        
	        //action listener for the reset button
	        Button_Reset.addActionListener(new ActionListener() {
	            @Override
	            public void actionPerformed(ActionEvent e) {
	                GUI.this.boardControl.resetBoard();
	                GUI.this.drawBoard();
	            }
	        });
	        
	        //action listener for the randomize button
	        Button_Rand.addActionListener(new ActionListener() {
	            @Override
	            public void actionPerformed(ActionEvent e) {
	                if(GUI.this.boardControl.isSolving()) return;
	                GUI.this.boardControl.randomizeBoard();
	                GUI.this.drawBoard();
	            }
	        });
	        
	        //action listener for the solve button
	        Button_Solve_A.addActionListener(new ActionListener() {
	            @Override
	            public void actionPerformed(ActionEvent e) {
	                if(GUI.this.boardControl.isSolving()) return;
	                GUI.this.boardControl.solve(GUI.this, Solvers.SOLVE_METHOD.A_STAR);
	                GUI.this.pack();
	                //GUI.this.setLocationRelativeTo(null);
	            }
	        });
	        
	        Button_Solve_DFS.addActionListener(new ActionListener() {
	            @Override
	            public void actionPerformed(ActionEvent e) {
	                if(GUI.this.boardControl.isSolving()) return;
	                GUI.this.boardControl.solve(GUI.this, Solvers.SOLVE_METHOD.DFS);
	                GUI.this.pack();
	                //GUI.this.setLocationRelativeTo(null);
	            }
	        });
	        
	        Button_Speed.addActionListener(new ActionListener(){
	            @Override
	            public void actionPerformed(ActionEvent e){
	                if(GUI.this.boardControl.isSolving()) return;
	                String crnt = ((JButton)e.getSource()).getText();
	                switch(crnt){
	                    case "Slow":
	                        GUI.this.boardControl.setTimerSpeed(BoardControl.SPEED.MEDIUM);
	                        GUI.this.Button_Speed.setText("Medium");
	                        break;
	                    case "Medium":
	                        GUI.this.boardControl.setTimerSpeed(BoardControl.SPEED.FAST);
	                        GUI.this.Button_Speed.setText("Fast");
	                        break;
	                    case "Fast":
	                        GUI.this.boardControl.setTimerSpeed(BoardControl.SPEED.SLOW);
	                        GUI.this.Button_Speed.setText("Slow");
	                        break;
	                }
	            }
	            
	        });
	        
	        this.drawBoard();
	        this.pack();                //resize the frame to fit the new size of the buttons
	    }
	    
	    //draw the current board on the tiles
	    public final void drawBoard(){
	        final byte[] board = boardControl.getCurrentBoard();
	        int empty = -1;
	        
	        //label the buttons (the tiles)
	        for(int i = 0 ; i < board.length ; ++i){
	            if(board[i] == 0) empty = i;
	            else tiles[i].setText(String.valueOf(board[i]));
	        }
	        
	        //show all the buttons then hide the blank one
	        for(JButton tile : tiles) tile.setVisible(true);
	        tiles[empty].setVisible(false);
	
	        //notify the panel to update
	        Main_Middle.repaint();
	        Main_Middle.revalidate();
	    }
	    
	    public void setStatus(String stat){
	        this.Label_Status.setText(stat);
	    }
	    
	    /**
	     * This method is called from within the constructor to initialize the form.
	     
	     */
	    @SuppressWarnings("unchecked")
	        private void initComponents() {
	        java.awt.GridBagConstraints gridBagConstraints;
	
	        jPanel1 = new javax.swing.JPanel();
	        Main_Right = new javax.swing.JPanel();
	        ButtonsPanel = new javax.swing.JPanel();
	        Button_Rand = new javax.swing.JButton();
	        Button_Reset = new javax.swing.JButton();
	        Button_Solve_A = new javax.swing.JButton();
	        Button_Solve_DFS = new javax.swing.JButton();
	        Button_Speed = new javax.swing.JButton();
	        jPanel2 = new javax.swing.JPanel();
	        Label_Status = new javax.swing.JLabel();
	        Main_Middle = new javax.swing.JPanel();
	        Tile_1 = new javax.swing.JButton();
	        Tile_2 = new javax.swing.JButton();
	        Tile_3 = new javax.swing.JButton();
	        Tile_4 = new javax.swing.JButton();
	        Tile_5 = new javax.swing.JButton();
	        Tile_6 = new javax.swing.JButton();
	        Tile_7 = new javax.swing.JButton();
	        Tile_8 = new javax.swing.JButton();
	        Tile_0 = new javax.swing.JButton();
	
	        setDefaultCloseOperation(javax.swing.WindowConstants.EXIT_ON_CLOSE);
	
	        Main_Right.setBorder(javax.swing.BorderFactory.createEmptyBorder(0, 10, 0, 10));
	        Main_Right.setLayout(new java.awt.BorderLayout());
	
	        ButtonsPanel.setOpaque(false);
	        ButtonsPanel.setLayout(new java.awt.GridBagLayout());
	
	        Button_Rand.setFont(new java.awt.Font("Ubuntu", 0, 16)); // NOI18N
	        Button_Rand.setText("Randomize");
	        Button_Rand.setFocusable(false);
	        gridBagConstraints = new java.awt.GridBagConstraints();
	        gridBagConstraints.fill = java.awt.GridBagConstraints.HORIZONTAL;
	        ButtonsPanel.add(Button_Rand, gridBagConstraints);
	
	        Button_Reset.setFont(new java.awt.Font("Ubuntu", 0, 16)); // NOI18N
	        Button_Reset.setText("Reset");
	        Button_Reset.setFocusable(false);
	        gridBagConstraints = new java.awt.GridBagConstraints();
	        gridBagConstraints.gridx = 0;
	        gridBagConstraints.gridy = 1;
	        gridBagConstraints.fill = java.awt.GridBagConstraints.HORIZONTAL;
	        ButtonsPanel.add(Button_Reset, gridBagConstraints);
	
	        Button_Solve_A.setFont(new java.awt.Font("Ubuntu", 0, 16)); // NOI18N
	        Button_Solve_A.setText("Solve - A*");
	        Button_Solve_A.setFocusable(false);
	        gridBagConstraints = new java.awt.GridBagConstraints();
	        gridBagConstraints.gridx = 0;
	        gridBagConstraints.gridy = 2;
	        gridBagConstraints.fill = java.awt.GridBagConstraints.HORIZONTAL;
	        ButtonsPanel.add(Button_Solve_A, gridBagConstraints);
	
	        Button_Solve_DFS.setFont(new java.awt.Font("Ubuntu", 0, 16)); // NOI18N
	        Button_Solve_DFS.setText("Solve - DFS");
	        Button_Solve_DFS.setFocusable(false);
	        Button_Solve_DFS.addActionListener(new java.awt.event.ActionListener() {
	            public void actionPerformed(java.awt.event.ActionEvent evt) {
	                Button_Solve_DFSActionPerformed(evt);
	            }
	        });
	        gridBagConstraints = new java.awt.GridBagConstraints();
	        gridBagConstraints.gridx = 0;
	        gridBagConstraints.gridy = 3;
	        gridBagConstraints.fill = java.awt.GridBagConstraints.HORIZONTAL;
	        ButtonsPanel.add(Button_Solve_DFS, gridBagConstraints);
	
	        Button_Speed.setText("Slow");
	        Button_Speed.setFocusable(false);
	        gridBagConstraints = new java.awt.GridBagConstraints();
	        gridBagConstraints.gridx = 0;
	        gridBagConstraints.gridy = 4;
	        gridBagConstraints.fill = java.awt.GridBagConstraints.HORIZONTAL;
	        ButtonsPanel.add(Button_Speed, gridBagConstraints);
	
	        Main_Right.add(ButtonsPanel, java.awt.BorderLayout.CENTER);
	
	        jPanel2.setBorder(javax.swing.BorderFactory.createEmptyBorder(10, 1, 1, 1));
	        jPanel2.setOpaque(false);
	        jPanel2.add(Label_Status);
	
	        Main_Right.add(jPanel2, java.awt.BorderLayout.PAGE_START);
	
	        getContentPane().add(Main_Right, java.awt.BorderLayout.LINE_END);
	
	        Main_Middle.setBorder(javax.swing.BorderFactory.createEmptyBorder(10, 10, 10, 10));
	        Main_Middle.setLayout(new java.awt.GridBagLayout());
	
	        Tile_1.setText("1");
	        gridBagConstraints = new java.awt.GridBagConstraints();
	        gridBagConstraints.gridx = 0;
	        gridBagConstraints.gridy = 0;
	        gridBagConstraints.fill = java.awt.GridBagConstraints.BOTH;
	        gridBagConstraints.ipadx = 50;
	        gridBagConstraints.ipady = 50;
	        Main_Middle.add(Tile_1, gridBagConstraints);
	
	        Tile_2.setText("2");
	        gridBagConstraints = new java.awt.GridBagConstraints();
	        gridBagConstraints.gridx = 1;
	        gridBagConstraints.gridy = 0;
	        gridBagConstraints.fill = java.awt.GridBagConstraints.BOTH;
	        gridBagConstraints.ipadx = 50;
	        gridBagConstraints.ipady = 50;
	        Main_Middle.add(Tile_2, gridBagConstraints);
	
	        Tile_3.setText("3");
	        gridBagConstraints = new java.awt.GridBagConstraints();
	        gridBagConstraints.gridx = 2;
	        gridBagConstraints.gridy = 0;
	        gridBagConstraints.fill = java.awt.GridBagConstraints.BOTH;
	        gridBagConstraints.ipadx = 50;
	        gridBagConstraints.ipady = 50;
	        Main_Middle.add(Tile_3, gridBagConstraints);
	
	        Tile_4.setText("4");
	        gridBagConstraints = new java.awt.GridBagConstraints();
	        gridBagConstraints.gridx = 0;
	        gridBagConstraints.gridy = 1;
	        gridBagConstraints.fill = java.awt.GridBagConstraints.BOTH;
	        gridBagConstraints.ipadx = 50;
	        gridBagConstraints.ipady = 50;
	        Main_Middle.add(Tile_4, gridBagConstraints);
	
	        Tile_5.setText("5");
	        gridBagConstraints = new java.awt.GridBagConstraints();
	        gridBagConstraints.gridx = 1;
	        gridBagConstraints.gridy = 1;
	        gridBagConstraints.fill = java.awt.GridBagConstraints.BOTH;
	        gridBagConstraints.ipadx = 50;
	        gridBagConstraints.ipady = 50;
	        Main_Middle.add(Tile_5, gridBagConstraints);
	
	        Tile_6.setText("6");
	        gridBagConstraints = new java.awt.GridBagConstraints();
	        gridBagConstraints.gridx = 2;
	        gridBagConstraints.gridy = 1;
	        gridBagConstraints.fill = java.awt.GridBagConstraints.BOTH;
	        gridBagConstraints.ipadx = 50;
	        gridBagConstraints.ipady = 50;
	        Main_Middle.add(Tile_6, gridBagConstraints);
	
	        Tile_7.setText("7");
	        gridBagConstraints = new java.awt.GridBagConstraints();
	        gridBagConstraints.gridx = 0;
	        gridBagConstraints.gridy = 2;
	        gridBagConstraints.fill = java.awt.GridBagConstraints.BOTH;
	        gridBagConstraints.ipadx = 50;
	        gridBagConstraints.ipady = 50;
	        Main_Middle.add(Tile_7, gridBagConstraints);
	
	        Tile_8.setText("8");
	        gridBagConstraints = new java.awt.GridBagConstraints();
	        gridBagConstraints.gridx = 1;
	        gridBagConstraints.gridy = 2;
	        gridBagConstraints.fill = java.awt.GridBagConstraints.BOTH;
	        gridBagConstraints.ipadx = 50;
	        gridBagConstraints.ipady = 50;
	        Main_Middle.add(Tile_8, gridBagConstraints);
	        gridBagConstraints = new java.awt.GridBagConstraints();
	        gridBagConstraints.gridx = 2;
	        gridBagConstraints.gridy = 2;
	        gridBagConstraints.fill = java.awt.GridBagConstraints.BOTH;
	        gridBagConstraints.ipadx = 50;
	        gridBagConstraints.ipady = 50;
	        Main_Middle.add(Tile_0, gridBagConstraints);
	
	        getContentPane().add(Main_Middle, java.awt.BorderLayout.CENTER);
	
	        pack();
	    }// GEN-END:initComponents
	
	    private void Button_Solve_DFSActionPerformed(java.awt.event.ActionEvent evt) {//GEN-FIRST:event_Button_Solve_DFSActionPerformed
	        // TODO add your handling code here:
	    }//GEN-LAST:event_Button_Solve_DFSActionPerformed
	
	    // Variables declaration - do not modify//GEN-BEGIN:variables
	    private javax.swing.JButton Button_Rand;
	    private javax.swing.JButton Button_Reset;
	    private javax.swing.JButton Button_Solve_A;
	    private javax.swing.JButton Button_Solve_DFS;
	    private javax.swing.JButton Button_Speed;
	    private javax.swing.JPanel ButtonsPanel;
	    private javax.swing.JLabel Label_Status;
	    public javax.swing.JPanel Main_Middle;
	    private javax.swing.JPanel Main_Right;
	    private javax.swing.JButton Tile_0;
	    private javax.swing.JButton Tile_1;
	    private javax.swing.JButton Tile_2;
	    private javax.swing.JButton Tile_3;
	    private javax.swing.JButton Tile_4;
	    private javax.swing.JButton Tile_5;
	    private javax.swing.JButton Tile_6;
	    private javax.swing.JButton Tile_7;
	    private javax.swing.JButton Tile_8;
	    private javax.swing.JPanel jPanel1;
	    private javax.swing.JPanel jPanel2;
	    // End of variables declaration//GEN-END:variables
	}
	


Analysis :
•	This is the GUI, this contain the output of the program, e.g. the title of the game, shape of board, buttons, etc.
