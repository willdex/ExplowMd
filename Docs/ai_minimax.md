# AI Minimax

## Algorithm
- Minimax with alpha-beta pruning
- Configurable depth (Easy=2, Medium=3, Hard=4)
- Time limit per difficulty (0.5s, 0.8s, 1.0s)

## Discretization
- ANGLE_STEP = 5°
- POWER_STEP = 0.1 (10% increments)

## State Evaluation Heuristics
- HP advantage: (myHP - enemyHP) * 100
- Distance penalty: |distance - optimalDistance| * 10
- Height advantage: position.height * 20
- Wind favorability per beast type
- State value: INTACT=50, WOUNDED=25, DYING=0

## Files
- MinimaxAI.cs: Core algorithm
- HeuristicEvaluator.cs: State scoring
- ActionGenerator.cs: {angle, power} combinations
- StateSimulator.cs: Predict outcome without game state change
- AIPlayer.cs: MonoBehaviour wrapper, Think() entry point

## Randomness
- AI_RANDOMNESS_EASY = 20°
- AI_RANDOMNESS_MEDIUM = 10°
- AI_RANDOMNESS_HARD = 2°