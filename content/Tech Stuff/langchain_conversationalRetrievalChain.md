---
title: "What is `ConversationalRetrievalChain`"
tags: ["paper", "rag"]
---

`ConversationalRetrievalChain` is a chain (a pipeline) in langchain that retrieve context from a  vector store to answer questions. So, it's essentially a **generator** module in RAG. A generator takes context as inputs, and generate a human-like answer.

In this paper, I summarize what has been known about it, and why you may want to customize it, and how.


# Under the hood

By the source [code](https://github.com/langchain-ai/langchain/blob/624554a43a1ab0113f3d79ebcbc9e726faecb339/langchain/chains/conversational_retrieval/base.py)

```python
class BaseConversationalRetrievalChain(Chain):
    """Chain for chatting with an index."""

    combine_docs_chain: BaseCombineDocumentsChain
    question_generator: LLMChain
    output_key: str = "answer"
    return_source_documents: bool = False
    get_chat_history: Optional[Callable[[CHAT_TURN_TYPE], str]] = None
    """Return the source documents."""

    class Config:
        """Configuration for this pydantic object."""

        extra = Extra.forbid
        arbitrary_types_allowed = True
        allow_population_by_field_name = True

    @property
    def input_keys(self) -> List[str]:
        """Input keys."""
        return ["question", "chat_history"]

    @property
    def output_keys(self) -> List[str]:
        """Return the output keys.

        :meta private:
        """
        _output_keys = [self.output_key]
        if self.return_source_documents:
            _output_keys = _output_keys + ["source_documents"]
        return _output_keys

    @abstractmethod
    def _get_docs(self, question: str, inputs: Dict[str, Any]) -> List[Document]:
        """Get docs."""

    def _call(
        self,
        inputs: Dict[str, Any],
        run_manager: Optional[CallbackManagerForChainRun] = None,
    ) -> Dict[str, Any]:
        _run_manager = run_manager or CallbackManagerForChainRun.get_noop_manager()
        question = inputs["question"]
        get_chat_history = self.get_chat_history or _get_chat_history
        chat_history_str = get_chat_history(inputs["chat_history"])

        if chat_history_str:
            callbacks = _run_manager.get_child()
            new_question = self.question_generator.run(
                question=question, chat_history=chat_history_str, callbacks=callbacks
            )
        else:
            new_question = question
        docs = self._get_docs(new_question, inputs)
        new_inputs = inputs.copy()
        new_inputs["question"] = new_question
        new_inputs["chat_history"] = chat_history_str
        answer = self.combine_docs_chain.run(
            input_documents=docs, callbacks=_run_manager.get_child(), **new_inputs
        )
        if self.return_source_documents:
            return {self.output_key: answer, "source_documents": docs}
        else:
            return {self.output_key: answer}

    @abstractmethod
    async def _aget_docs(self, question: str, inputs: Dict[str, Any]) -> List[Document]:
        """Get docs."""

    async def _acall(
        self,
        inputs: Dict[str, Any],
        run_manager: Optional[AsyncCallbackManagerForChainRun] = None,
    ) -> Dict[str, Any]:
        _run_manager = run_manager or AsyncCallbackManagerForChainRun.get_noop_manager()
        question = inputs["question"]
        get_chat_history = self.get_chat_history or _get_chat_history
        chat_history_str = get_chat_history(inputs["chat_history"])
        if chat_history_str:
            callbacks = _run_manager.get_child()
            new_question = await self.question_generator.arun(
                question=question, chat_history=chat_history_str, callbacks=callbacks
            )
        else:
            new_question = question
        docs = await self._aget_docs(new_question, inputs)
        new_inputs = inputs.copy()
        new_inputs["question"] = new_question
        new_inputs["chat_history"] = chat_history_str
        answer = await self.combine_docs_chain.arun(
            input_documents=docs, callbacks=_run_manager.get_child(), **new_inputs
        )
        if self.return_source_documents:
            return {self.output_key: answer, "source_documents": docs}
        else:
            return {self.output_key: answer}

    def save(self, file_path: Union[Path, str]) -> None:
        if self.get_chat_history:
            raise ValueError("Chain not savable when `get_chat_history` is not None.")
        super().save(file_path)


class ConversationalRetrievalChain(BaseConversationalRetrievalChain):
    """Chain for chatting with an index."""

    retriever: BaseRetriever
    """Index to connect to."""
    max_tokens_limit: Optional[int] = None
    """If set, restricts the docs to return from store based on tokens, enforced only
    for StuffDocumentChain"""

    def _reduce_tokens_below_limit(self, docs: List[Document]) -> List[Document]:
        num_docs = len(docs)

        if self.max_tokens_limit and isinstance(
            self.combine_docs_chain, StuffDocumentsChain
        ):
            tokens = [
                self.combine_docs_chain.llm_chain.llm.get_num_tokens(doc.page_content)
                for doc in docs
            ]
            token_count = sum(tokens[:num_docs])
            while token_count > self.max_tokens_limit:
                num_docs -= 1
                token_count -= tokens[num_docs]

        return docs[:num_docs]

    def _get_docs(self, question: str, inputs: Dict[str, Any]) -> List[Document]:
        docs = self.retriever.get_relevant_documents(question)
        return self._reduce_tokens_below_limit(docs)

    async def _aget_docs(self, question: str, inputs: Dict[str, Any]) -> List[Document]:
        docs = await self.retriever.aget_relevant_documents(question)
        return self._reduce_tokens_below_limit(docs)

    @classmethod
    def from_llm(
        cls,
        llm: BaseLanguageModel,
        retriever: BaseRetriever,
        condense_question_prompt: BasePromptTemplate = CONDENSE_QUESTION_PROMPT,
        chain_type: str = "stuff",
        verbose: bool = False,
        combine_docs_chain_kwargs: Optional[Dict] = None,
        **kwargs: Any,
    ) -> BaseConversationalRetrievalChain:
        """Load chain from LLM."""
        combine_docs_chain_kwargs = combine_docs_chain_kwargs or {}
        doc_chain = load_qa_chain(
            llm,
            chain_type=chain_type,
            verbose=verbose,
            **combine_docs_chain_kwargs,
        )
        condense_question_chain = LLMChain(
            llm=llm, prompt=condense_question_prompt, verbose=verbose
        )
        return cls(
            retriever=retriever,
            combine_docs_chain=doc_chain,
            question_generator=condense_question_chain,
            **kwargs,
        )
```

, you can see





