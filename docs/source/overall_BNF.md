# Overall BNF

```
<Scenario> ::= scenario_top: <GlobalConfig>+ ; 
               (<AgentDecl>+ | <Orchestration>) <AdaptProfile>*
<GlobalConfig> ::= <MapConfig> | <EnvConfig> | <VarDecl>
<MapConfig> ::= path: Path; path.set_map(<String>);(path.<MapMethod>(<Args>);)*
<MapMethod> ::= min_lanes | create_route | (<ID>)
<EnvConfig> ::= env.<EnvAttr>(<Val>);*
<EnvAttr>   ::= clouds | rain | fog | air | wind
<AgentDecl> ::= <ID>:<ModelName>
<AdaptProfile> ::= struct <ID>; 
                   def <ID>((<ParamDecl>)+) is <Branch>
<ParamDecl> ::= <ID>:<Type>
<Branch> ::= if <Cond>:<Choose> 
            (elif <Cond>:<Choose>)* 
            else:<Choose>
<Choose> ::= choose: {<Option>}+
<Option> ::= {model=<ModelParams>; logic={logic_params: <LogicParams>}}
<Orchestration> ::= <BatchBinding>* <Execution>
<BatchBinding> ::= <ID>:<Type>=<Expr>
                   auto_orchestrates_behavior(<ID>, <ID>)
<Execution> ::= do (serial | parallel):<Content>
<Content> ::= (<Phase> | <Action> | <Nested>)+
<Nested> ::= (serial | parallel):(<Content>)
<Action> ::= <ID>.<Verb>(<Arg>*) with: <BehProfile>*
<Verb> ::= drive | walk
<BehProfile> ::= set_behavior_model(<ModelParams>) | set_behavior_logic(<LogicParams>)
<ModelParams> ::= behavior_type:<Type>, 
                  model:<ID>, 
                  hyperparameters:<Val>
<LogicParams> ::= (<GeneralModifier> | <DirectiveModifier>)* 
<GeneralModifier> ::= <GenKey>: "<GenVal> | <Temporal>"
<GenKey>        ::= position | lane | yaw | orientation
<GenVal>        ::= int | [Int] | <ID>
<Temporal>      ::= at: (start | end | all)
<DirectiveModifier> ::= <DirName>(<Args>)
<DirName>          ::= change_lane | follow_lane | change_speed | keep_speed
<Args> ::= <Param>(, <Param>)*
<Param> ::= <Key>:<Val>
<Val>   ::= <Int> | <String> | <ID>
<GenVal> ::= <Int> | [<Int>..<Int>]
```

